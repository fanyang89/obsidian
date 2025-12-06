#数据库
你这样完全没用上索引，你这个场景，最好就使用 postgresql ，使用 array 或者 json 存 tags ，然后建立 GIN 索引，然后每次查询把标签和子标签组成目标 tags ，然后用

```
select * from xxx where tags @> ARRAY[tag1,tag2,tag3]
```

就可以用上 GIN 索引，一个亿数据也没啥
