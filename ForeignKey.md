# ORM的一对多

## 1. 一对多的正向查找

**正向查：**ForeignKey在UserInfo表里，如果根据UserInfo这张表去查询这两张关联的表的合起来的内容就是正向查

**反向查：**ForeignKey不在UserType里,如果根据UserType这张表去查询这两张关联的表的合起来的内容就是反向查

**正向查询**
1. 跨表查询

假设由两张表,作者表,书籍表,书籍表中user字段外键关联作者表id,现查询所有男性作者的数据？

    ret = models.Book.objects.filter(user__sex=True)

* 使用 **双下划线 + 属性** 筛选查询


**反向查询**
1. 主表查询子表

假设由两张表,作者表,书籍表,书籍表中user字段外键关联作者表id,一名作者的所有书籍？

    user = models.User.objects.get(id=1)

* 主表.子表_set()

        user.Book_set().all()

* 通过在外键中设置related_name属性值, 外键设置*related_name='books'*

        user.books.all()

* 通过@property装饰器在model中预定义方法实现

        user.all_books

        # User表中添加方法
        @property
        def all_books(self):
            return books.all()
