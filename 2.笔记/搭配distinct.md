#2-未上传 

```mysql
# 和distinct搭配
select 
   SUM(DISTINCT salary),
   SUM(salary)
from
   employees;
```