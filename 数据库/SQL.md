* where 中不能使用聚合函数（max，min，sum，count等），增加having子句

  ```sql
  --SQL HAVING 语法
  SELECT column_name, aggregate_function(column_name)
  FROM table_name
  WHERE column_name operator value
  GROUP BY column_name
  HAVING aggregate_function(column_name) operator value
  ```

* 用group by可以去除重复的元素

