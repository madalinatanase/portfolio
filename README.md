# SQL code samples


## Sample 1

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
## Sample 2

Here I created a virtual table to help me keep data as neatly as possible. 

I used this to create income segments based on certain selected countries.

```
with virtual_table as 
    (select count(*) as count, country,
    case when count(*)>1000000 then '1m and over'
    when count (*)>100000 then '100k -1m'
    when count (*)>10000 then '10k - 100k'
    when count (*)>1000 then '1k-10k'
    else 'under 1k'
    end as country_tiers
    from users 
    where country is not null
    group by country
    order by count(*) desc)
select country_tiers, count(*)`
from virtual_table 
group by country_tiers

```
## Sample 3

Here I used a window function instead of the group by function to partition top values in different categories

```
with base as (
    select DT,USER_ID,IS_ACTIVE,OUTBOUND_TRANSACTIONS_VOLUME,
    row_number()over(partition by user_id order by DT desc) as rank_partition
    from user_activity
    where DT > '2020-11-01'
    and IS_ACTIVE=true
    )
select * from base
where rank_partition=1

```
## Sample 4

```
with base as (
    select Country,City,count(*) as COUNT
    from users
    group by Country,City
    )
, base2 as (
    select country,city,count,
    row_number()over (order by count desc) as rank
    from base
    where country is not null
    order by count desc)
    
select country,count(*)
from base2
where rank<=100
group by 1
order by count(*) desc
limit 100 

```
## Sample 5

```
with base as (
    select owner_id, count(distinct currency) as currency_count
    from pockets
    where pockets_state='ACTIVE'
    group by 1
)

select count(*),
case when currency_count=3 then '3'
when currency_count=2 then '2'
when currency_count=1 then '1'
else 'above 3' end
from base
group by 2

```
## Sample 6

```
with base as (
    select ea_merchant_name,count(*) as COUNT,
    case
    when count(*)>1000000 then 'over 1mil'
    when count(*) = 1000000 then '1 million'
    when count(*)>100000 then '100k-1mil'
    when count(*)>10000 then '10k-100k'
    when count(*)>1000 then '1k-10k'
    when count(*)<=1000 then 'less than 1k'
    else 'not applicable' end as CAT2
    from transactions_b
    where created_date>'2020-11-01' and
    transactions_type='CARD_PAYMENT'
    and ea_merchant_name is not null
    group by ea_merchant_name
    order by count(*) desc
    )
select CAT2,count(*)
from base
GROUP by 1
order by cat2 desc
limit 50

```
## Sample 7

```
with base as (
    select sum(balance) as total_balance,currency,owner_id
    from users
    join pockets on users.id=pockets.owner_id
    where currency in ('GBP','EUR')
    and country='GB'
    group by 2,3
    )
select avg(total_balance),currency
from base
group by currency
limit 40

```
