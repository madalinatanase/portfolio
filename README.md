# SQL code sample


## Example 1

Here I made a virtual table to help me narrow down my columns (and rows) to a minimum so I can be more effective.

I prefer using CTEs (common table expressions) to making nested queries because they are easier to read and reuse.
```
with base as (
    select age_group,country,count(*) as COUNT 
    from users where age_group is not null
    and country is not null
    group by 1,2)

, base_2 as (
    select country,age_group,COUNT, 
    row_number()over(partition by country order by COUNT desc) as Hierarchy
    from BASE
    )
select * from base_2
where country in ('RO','GB','FR','DE')
order by Hierarchy
limit 100
```
