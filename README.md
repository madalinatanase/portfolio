# SQL code sample


## Example 1
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
