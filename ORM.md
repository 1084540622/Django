# ORM
## 对象-关系映射

## 一. ORM介绍

### 1. ORM优势

（1）只需要面向对象编程, 不需要面向数据库编写代码.

- 对数据库的操作都转化成对类属性和方法的操作.
- 不用编写各种数据库的sql语句.

（2）实现了数据模型与数据库的解耦, 屏蔽了不同数据库操作上的差异.

- 不在关注用的是mysql、oracle...等.
- 通过简单的配置就可以轻松更换数据库, 而不需要修改代码.


### 2. ORM劣势

- 相比较直接使用SQL语句操作数据库,有性能损失.
- 根据对象的操作转换成SQL语句,根据查询的结果转化成对象, 在映射过程中有性能损失.

## 二. ORM用法

![关系图](https://github.com/1084540622/Django/blob/master/templates/ORM%E4%BA%8E%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E5%85%B3%E7%B3%BB.png)

### 1. 字段类型：
**属性名 = models.字段类型**,定义属性时需要指定字段类型, 通过字段类型的参数指定选项

**属性名**
- 不允许使用python的保留关键字
- 不允许使用mysql的保留关键字
- 不允许使用连续的下划线，因为Django的查询语法就是连续的下划线

**1.字段类型**

| 字段 | 说明 |
|:----|:----:|
|AutoField|自动增长的IntegerField, 不指定时Django会自动创建属性名为id的自动增长属性|
|BooleanField|布尔字段，值为True或False|
|NullBooleanField|支持Null、True、False三种值|
|CharField(max_length=20)|字符串|
|TextFiled|大文本字段，一般超过4000个字符时使用|
|IntegerField|整数|
|DecimalField(max_digits=None, decimal_places=None)|可以指定精度的十进制浮点数|
|FloatField|浮点数|
|DateField(auto_now=False, auto_now_add=False)|日期|
|TimeField|参数和DateField一样|
|DateTimeField|日期时间，参数同DateField|
|FileField|上传文件字段，以二进制的形式|
|ImageField|继承于FileField，对上传的内容进行校验，确保是有效的图片|

* 参数max_length表示最大字符个数
* 参数max_digits表示总位数
* 参数decimal_places表示小数位数
* 参数auto_now表示每次保存对象时，自动设置该字段为当前时间，用于"最后一次修改"的时间戳，它总是使用当前日期，默认为false
* 参数auto_now_add表示当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前日期，默认为false
* 参数auto_now_add和auto_now是相互排斥的，组合将会发生错误


**2.字段选项**

| 参数 | 说明 |
|:----|:----:|
|null|如果为True，表示允许为空，默认值是False|
|blank|如果为True，则该字段允许为空白，默认值是False|
|db_column|字段的名称，如果未指定，则使用属性的名称（只限于数据库表中的名字，操作数据库还是类属性的名字）|
|db_index|若值为True, 则在表中会为此字段创建索引，默认值是False（为了优化查询速度 ）|
|default|默认值，这可以是值或可调用对象。如果可调用，则每次创建新对象时都会调用它|
|primary_key|若为True，则该字段会成为模型的主键字段，默认值是False，一般作为AutoField的选项使用|
|unique|如果为True, 这个字段在表中必须有唯一值，这个值不能重复，默认值是False|
|choices|django admin中显示下拉框，避免连表查询(连表查询效率低)|


* null是数据库范畴的概念，blank是表单验证范畴的
* choices=((k,v),(k,v)), 可以是使用*对象.get_字段名_display()*这种方式获取v


### 2. 查询方法

1. **all()**
* 查询所有结果

2. **get(\*\*kwargs)**
* 返回与所给筛选条件相匹配的对象，返回结果有且只有一个.
* 如果符合筛选条件的对象超过一个或者没有，都将抛出异常.

3. **filter(\*\*kwargs)**
* 返回所有符合筛选条件的对象

4. **exclude(\*\*kwargs)**
* 返回所有不符合筛选条件的对象

5. **values(\*field)**
* 返回一个ValueQuerySet（一个特殊的QuerySet）.
* 运行后得到的并不是一系列model的实例化对象，而是一个可迭代的字典序列.

6. **values_list(\*field)**
* 它与values()非常类似，它返回的是一个元组序列，values返回的是一字典序列.

7. **order_by(\*field)**
* 对查询结果排序.
* 参数接收字符串类型的字段，指定按某个字段排序.
* 使用负号(例如"-字段名")可以实现降序.

8. **reverse()**
* 对查询结果反向排序.
* 通常只能在具有已定义顺序的QuerySet上调用（在model类的Meta中指定ordering或调用order_by()方法）.

9. **distinct()**
* 从返回结果中剔除重复记录.
* 如果查询操作跨越多个表，可能在计算QuerySet时得到重复的结果，此时可以使用distnct()，注意只有在PostgreSQL中支持按字段去重.
* 它是对整个对象的去重，不是对数据去重.

10. **count()**
* 返回数据库中匹配查询(QuerySet)的对象那个数量.

11. **first()**
* 返回第一条记录.

12. **last()**
* 返回最后一条记录

13. **exists()**
* 如果QuerySet包含数据返回True，否则False.


### 3. 神奇的双下划线

1. filter(id__gt=2, id__lt=4)
* 获取id 大于2 且 小于4 的对象.

2. filter(id__gte=2, id__lte=4)
* 获取id 大于等于2 且 小于等于4 的对象.

3. filter(id__in=[2,4])
* 获取所有id等于2，4的数据.

4. filter(name_contains='yk')
* 获取所有name字段包含’yk’的对象.

5. filter(name_icontains='yk')
* 获取所有name字段包含’yk’的对象，忽略大小写.

6. filter(id__range=[1,3])
* 获取id范围在1-3的对象，顾头顾尾.

7. filter(date__year=2018)
* 获取date字段时间为2018年的对象.

8. __isnull = True
* 判断字段是否为空, 为空返回True.

> 类似的还有：startswith, istartswith, endswith, iendswith