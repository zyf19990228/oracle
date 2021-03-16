# test1

>   **姓名**：张耀飞
>
>   **实验目的**：分析SQL执行计划，执行SQL语句的优化指导。理解分析SQL语句的执行计划的重要作用。
>
>   **实验内容**：
>
>   -   对Oracle12c中的HR人力资源管理系统中的表进行查询与分析。
>   -   首先运行和分析教材中的样例：本训练任务目的是查询两个部门('IT'和'Sales')的部门总人数和平均工资，以下两个查询的结果是一样的。但效率不相同。
>   -   设计自己的查询语句，并作相应的分析，查询语句不能太简单。

## **查询语句一**

```sql
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT','Sales')
GROUP BY d.department_name;
```

> **分析**： 先由where子句筛选符合条件（两个部门id相等d.department_id = e.department_id，并且是IT或Sales部门）的所有匹配项，再由group by按部门名分组。最后聚合函数count统计人数、avg计算平均工资！

## **查询语句二**

```sql
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
FROM hr.departments d,hr.employees e
WHERE d.department_id = e.department_id
GROUP BY d.department_name
HAVING d.department_name in ('IT','Sales');
```

> **分析**：依然由where子句筛选部门id相等的项（d.department_id = e.department_id），但是没有做部门判断，接着由group by按部门名分组，然后对每个分组筛选部门再IT或Sales中的项，最后由聚合函数
count统计人数、avg计算平均工资！
***
> **总结**：语句二执行效率要高于查询语句一。对于查询语句一，每条匹配项都会判断部门是否再条件里，数据量大了后，明显会降低查询效率！而对于语句二，选择的是对分组后的项进行部门判断！
> **Sqldeveloper优化指导：** There are no recommendations to improve the statement.

***

## **自己设计查询语句**

```sql
set autotrace on

select d.department_name, count(*) as "部门总人数",
avg(e.salary) as "平均工资"
from hr.departments d, hr.employees e
where d.department_id = e.department_id
GROUP BY d.department_name
HAVING d.department_name in ('IT','Sales');
```

> **分析**: 统计部门人数的地方直接统计全部的人数即可,因为已经分组并且筛选结束!