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
