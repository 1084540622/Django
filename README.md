# Django

## Serializer 
Serializer 允许复杂数据（比如 querysets 和 model 实例）转换成python数据类型，然后可以更容易的转换成 json 或 xml 等。同时，Serializer也提供了反序列化功能，允许解析数据转换成复杂数据类型。

### 声明 Serializers
我们来创建一个简单对象作为例子：

    from datetime import datetime

    class Comment(object):
        def __init__(self, email, content, created=None):
          self.email = email
          self.content = content
          self.created = created or datetime.now()

    comment = Comment(email='leila@example.com', content='foo bar')
    
然后我们声明一个serializer ：

    from rest_framework import serializers

    class CommentSerializer(serializers.Serializer):
        email = serializers.EmailField()
        content = serializers.CharField(max_length=200)
        created = serializers.DateTimeField()
        
### 序列化对象
    
    serializer = CommentSerializer(comment)
    serializer.data
    # {'email': 'leila@example.com', 'content': 'foo bar', 'created': '2016-01-27T15:17:10.375877'}
    
这时我们已经转换model 实例成了python 数据类型，接下来我们将它转换成json 格式

    from rest_framework.renderers import JSONRenderer

    json = JSONRenderer().render(serializer.data)
    json
    # b'{"email":"leila@example.com","content":"foo bar","created":"2016-01-27T15:17:10.375877"}'
    
### 反序列化对象
反序列化就是将json或xml等格式转换成复杂数据类型

    serializer = CommentSerializer(data=data)
    serializer.is_valid()
    # True
    serializer.validated_data
    # {'content': 'foo bar', 'email': 'leila@example.com', 'created': datetime.datetime(2012, 08, 22, 16, 20, 09, 822243)}
    
### 验证
反序列化数据的时候，在访问有效数据或保存对象实例之前，你应该总是调用 is_valid() ，以查看是否反序列化成功，如果有错误， .errors 属性包含错误信息。

    serializer = CommentSerializer(data={'email': 'foobar', 'content': 'baz'})
    serializer.is_valid()
    # False
    serializer.errors
    # {'email': [u'Enter a valid e-mail address.'], 'created': [u'This field is required.']}
    
### 保存实例
如果你希望返回基于验证数据(validated data，也就是反序列化后的数据)的完整的对象实例，你需要实现.create() 或 .update() 方法。

    class CommentSerializer(serializers.Serializer):
    email = serializers.EmailField()
    content = serializers.CharField(max_length=200)
    created = serializers.DateTimeField()

    def create(self, validated_data):
        return Comment(**validated_data)

    def update(self, instance, validated_data):
        instance.email = validated_data.get('email', instance.email)
        instance.content = validated_data.get('content', instance.content)
        instance.created = validated_data.get('created', instance.created)
        return instance
        
现在，当你反序列化数据时，你需要调用.save() 方法返回对象实例，基于验证的数据(validated data)

    comment = serializer.save()
    
调用save() 时，要么创建一个新实例，要么更新一个已存在的实例，这取决于你是否传入了实例参数

    # .save() will create a new instance.
    serializer = CommentSerializer(data=data)

    # .save() will update the existing `comment` instance.
    serializer = CommentSerializer(comment, data=data)
