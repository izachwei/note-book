MySQL **NOT IN 通常不会走索引**
可以通过 LEFT JOIN WHERE IS NULL / NOT EXIST 做优化

原始版本
```
select
	*
from
	t_user tu
where
	tu.id not in (
	select
		id
	from
		t_user_b tub)
```

优化版本
```
select
	*
from
	t_user tu
left join t_user_b tub on
	tu.id = tub.id
where
	tub.id is null
```
