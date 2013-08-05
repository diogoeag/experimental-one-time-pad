### Queries

bla bla

```java
Expression query =
	select(all())
	.from(table("streams"));

List<Map<String, ResultColumn>> results = engine.query(query);

for(Map<String, ResultColumn> result : results) {
	System.out.println(result.get("id").toInt() + ": " + result.get("description").toString());
}
```

bla bla

```java
results = engine.query(
	select(all())
	.from(table("streams")));
```

	select
	- bla bla

	.distinct()
	- bla bla
	
	.from
	- bla bla

bla bla

	all()
	- bla bla

	k()
	- bla bla
	
	column()
	-bla bla

bla bla

```java
results = engine.query(
	select(all())
	.from(table("streams"))
	.where(eq(column("data_type_id"), k(4))));
```

	.where()
	-bla bla

bla bla

	eq()
	-bla bla
	

bla bla

```java
results = engine.query(
	select(all())
	.from(table("streams"))
	.where(
		and(
			eq(column("data_type_id"), k(4))),
			eq(column("provider_id"), k(1))))));
```

bla bla

```java
results = engine.query(
	select(all())
	.from(table("streams"))
	.where(
		and(
			eq(column("data_type_id"), k(4))),
			eq(column("provider_id"), k(1))))));
```

bla bla

```java
results = engine.query(
	select(column("data_type_id"), count(column("id")).alias("count")))
	.from(table("streams"))
	.where(eq(column("data_type_id"), k(4)))
	.groupby(column("data_type_id"))
	.orderby(asc()));
```

bla bla

groupby
orderby
having
limit
offset

table

and
or

in
notIn
between
notBetween

minus
mult
plus
div
mod

max
min
stddev
avg
count
sum

udf

coalesce

lit

alias

asc
desc

innerJoin
leftOuterJoin
rightOuterJoin
fullOuterJoin
join
