---
title: mysql 分页数据重复
date: 2018-02-01 15:17:34
tags: mysql
---

**BUG mysql分页数据偶尔有重复**

解决：在sql语句中使用了order by field，其中filed字段非id，数据有重复导致分页数据偶尔会重复，再增加order by id可以解决，参考[stackoverflow](https://stackoverflow.com/questions/43798247/laravel-pagination-showing-duplicate-and-replacing-random-row)
以及 [stackoverflow](https://stackoverflow.com/questions/6662837/how-mysql-order-the-rows-with-same-values)