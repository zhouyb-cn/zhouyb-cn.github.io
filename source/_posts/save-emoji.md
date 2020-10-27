---
title: 数据库保存emoji表情
date: 2017-03-06 11:56:01
tags: php
---
#### 问题
mysql保存emoji表情的时候，插入到数据库为问号或者无法插入
#### 原因
是由于mysql的utf-8编码最多三个字节，而emoji表情是四个字节，导致插入错误
#### 解决方案
**修改数据库设置字符集**

``` sql
ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;
```

**修改数据表字符集**

``` sql
ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

**修改字段名字符集**

``` sql
ALTER TABLE table_name CHANGE column_name VARCHAR(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

**数据库连接方式参数设置**
在laravel框架中修改database.php

``` php
'charset'   => 'utf8mb4',
'collation' => 'utf8mb4_unicode_ci',
```

