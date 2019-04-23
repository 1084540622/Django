# Django

## Serializer 
Serializer 允许复杂数据（比如 querysets 和 model 实例）转换成python数据类型，然后可以更容易的转换成 json 或 xml 等。同时，Serializer也提供了反序列化功能，允许解析数据转换成复杂数据类型。

### 声明 Serializers
我们来创建一个简单对象作为例子：
''' python
  from datetime import datetime

  class Comment(object):
      def __init__(self, email, content, created=None):
          self.email = email
          self.content = content
          self.created = created or datetime.now()

  comment = Comment(email='leila@example.com', content='foo bar')
'''
