关于Django执行原生SQL查询参数问题

#### 开端

最近跟同事吃饭时，同事说遇到一个奇怪的问题，在使用Django执行原生查询的时候得到了意想不到的结果，排查两小时无果，听起来比较好奇，就想探究下，下面记录下探索过程。

#### 问题描述

很简单的一个sql类似这样

```sh
// SELECT * FROM myapp_person WHERE id in (...)

// 1. 转换成Django查询语法可以这么写
>>> Person.objects.raw('SELECT * FROM myapp_person WHERE id in (%s)'% ('1,2,3'))
>>> print(qs.query)
SELECT * FROM myapp_person WHERE id in (1,2,3)

// 2. 按照django文档也可以使用params参数
>>> Person.objects.raw('SELECT * FROM myapp_person WHERE id in (%s)', ['1,2,3'])
>>> print(qs.query)
SELECT * FROM myapp_person WHERE id in (1,2,3)
```

可以看到两种方式打印的sql都是相同的，实际上执行结果并非如此，方式1会返回3条记录，方式2只会返回一条记录

<!--python版本 3.7.9，Django版本 2.2.4-->

#### 思考

很简单的一个sql语句，打印出的sql都是一样的，也看了官方文档[传参数给ra w()](https://docs.djangoproject.com/zh-hans/4.0/topics/db/sql/)，并没有发现有什么不对，既然问题出现在raw方法的参数上，猜想Django在最后拼接sql的时候跟打印出来的不同，想到Django里面有个connection可以打印执行过的sql

```python
from django.db import connection
print(connection.queries)
# SELECT * FROM myapp_person WHERE id in ('1,2,3')
```

找到问题了，实际上执行的sql参数当成一个字符串了，所以只返回一条记录，

下面就是看下具体处理过程

#### 看源码

```python
print(qs.query)
```

这个打印最终执行只是简单的字符串拼接

```python
    def __str__(self):
        """
        Return the query as a string of SQL with the parameter values
        substituted in (use sql_with_params() to see the unsubstituted string).

        Parameter values won't necessarily be quoted correctly, since that is
        done by the database interface at execution time.
        """
        sql, params = self.sql_with_params()
        return sql % params
```

实际上最终sql执行是在 .../lib/site-packages/MySQLdb/cursors.py文件中的execute方法

```python
        if args is not None:
            if isinstance(args, dict):
                nargs = {}
                for key, item in args.items():
                    if isinstance(key, unicode):
                        key = key.encode(db.encoding)
                    nargs[key] = db.literal(item)
                args = nargs
            else:
                args = tuple(map(db.literal, args))
            try:
                query = query % args
            except TypeError as m:
                raise ProgrammingError(str(m))
```

其中关键的一句 **args = tuple(map(db.literal, args))**

通过追源码可以看到最终在这里格式化参数的时候加了单引号"'%s'" % escape_string(str(obj))

```python
    def string_literal(self, obj): # real signature unknown; restored from __doc__
        """
        string_literal(obj) -- converts object obj into a SQL string literal.
        This means, any special SQL characters are escaped, and it is enclosed
        within single quotes. In other words, it performs:

        "'%s'" % escape_string(str(obj))

        Use connection.string_literal(obj), if you use it at all.
        _mysql.string_literal(obj) cannot handle character sets.
        """
        pass
```

所以最终执行的sql变成了

SELECT * FROM myapp_person WHERE id in ('1,2,3')

感觉框架给封装好了，一些细节就不会太注意，实际使用过程中还是会碰到一些莫名其妙的问题需要深究下

ORM包降低了使用门槛，但是毕竟不是特别全，像这次qs.query方法还是不太可信，这种很难避免，一个orm包一个驱动实现没办法做到完全一致

这里简单做下记录，方便以后回顾。