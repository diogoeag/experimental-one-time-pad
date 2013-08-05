### Selection Queries

Now things will get interesting. In this section we will see how PDB uses SQL to select data from the database.

```java
Expression query =
	select(all())
	.from(table("streams"));

List<Map<String, ResultColumn>> results = engine.query(query);

for(Map<String, ResultColumn> result : results) {
	System.out.println(result.get("id").toInt() + ": " + result.get("description").toString());
}
```

	.query(Query query)
	- Processes a given query and computes the corresponding result.
	- It return a List of results if any.
	- For each column a result is a Map that maps column names to ResultColumn objects.

	.toXXX()
	- ResultColumn provides methos to convert the data to the type of your preferebce.
	- Be aware that it might give an exception if you try to convert the underlying data to some incompatible type.

Lets see this simple query in more detail.
Where we list all entries in table Streams and return all columns.

```java
results = engine.query(
	select(all())
	.from(table("streams")));
```

	select()
	- bla bla

	.distinct()
	- bla bla
	
	.from()
	- bla bla

bla bla

	all()
	- bla bla

	k()
	- bla bla

	lit()
	- bla bla
	
	column()
	- bla bla

bla bla

```java
results = engine.query(
	select(all())
	.from(table("streams"))
	.where(eq(column("data_type_id"), k(4)))
	.andWhere(like(column("description"), k("match text"))));
```

	.where()
	- bla bla
	
	.andWhere()
	- bla bla

bla bla

	eq()
	- bla bla
	
	neq()
	- bla bla

	like()
	- bla bla

	lt()
	lteq()
	gt()
	gteq()
	- bla bla

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

	and()
	or()
	- bla bla

	between()
	notBetween()
	- bla bla

	in()
	notIn()
	- bla bla

	L()
	- bla bla

It is widely known that greater the id greater the Stream of data.
For this porpuse you just design a query that selects the maximum Stream id of data Type 4 from Provider 1. 
You might just get a raise for this.

```java
results = engine.query(
	select(max(column("id")).alias("the_best"))
	.from(table("streams"))
	.where(
		and(
			eq(column("data_type_id"), k(4))),
			eq(column("provider_id"), k(1))))));
```

	.alias(String alias)
	- Assigns an alias to the expression.
 
	count(Expression expression)
	max(Expression expression)
	min(Expression expression)
	sum(Expression expression)
	avg(Expression expression)	
	stddev(Expression expression)
	- Aggregation operator for numeric values.
	- They are aplicable to expression involving columns.

	udf(String name, Expression exp)
	- If you have defined your own sql function you may access it with udf

bla bla

```java
results = engine.query(
	example);
```

bla bla

	innerJoin()
	leftOuterJoin()
	rightOuterJoin()
	fullOuterJoin()
	- bla bla

	.join()
	- bla bla

The market is collapsing! The reason some say is that some provider messed up.
In your contract it says that Provider with id 4 provides a given number of streams for each data_type.
With the following query you will fin dout if the actual data in the database matches the contract.
By filtering the results to only account for Provider 4 and grouping on the data Type you are able to count the number of streams by Type.
You Boss will be pleased.

```java
results = engine.query(
	select(column("data_type_id"), count(column("id")).alias("count"))
	.from(table("streams"))
	.where(eq(column("provider_id"), k(4)))
	.groupby(column("data_type_id"))
	.orderby(column("data_type_id")).asc());
```

	.groupby(Expression... groupbyColumns)
	- Groups the result on some of the table columns.
	
	.orderby(Expression... orderbyColumns)
	- Orders the result according to some expression of the table columns.

	.asc()
	.desc()
	- Sets the ordering of your choice, either ascendent or descendent.

Some documents leaked online last week sugest that there are some hidden message in our data.
To visualize this hidden message we need to do some aritmetics with the ids of the provider and data_type on table Streams.
Even more strange is the need to filter the description column, where in case of a null value an altenative is presented.

```java
results = engine.query(
	select(
		plus(
			column("data_type_id"),
			column("provider_id"),
			k(1)
		).alias("mess_ids"),
		coalesce(
			column("description"),
			k("Romeo must die")))
	.from(table("streams")));
```

	minus(Expression... expressions)
	mult(Expression... expressions)
	plus(Expression... expressions)
	div(Expression... expressions)
	mod(Expression... expressions)
	- Operators to manipulate numeric values.
	- Applies the respective operator to the list of value with left precedence.

	coalesce(Expression expression, Expression... alternatives)
	- Coalesce tests a given expression and returns its value if it is not null.
	- If the primary expression is null, it will return the first alternative that is not.

For this next example, imagine you want to select all Streams for which the sum of data_type_id and provider_id is greater than 5.
It might not be a very usefull query, but when you had that you just want 10 rows of the result with and offset of 2, people might wonder what you are up to.

```java
results = engine.query(
	select(all())
	.from(table("streams"))
	.having(
		gt(
			plus(
				column("data_type_id"),
				column("provider_id")),
			k(5)))
	.limit(10)
	.offset(2));
```

	having(Expression having)
	- Query will select only the result rows where aggregate values meet the specified conditions.

	limit(Integer limit)
	- Defines the number of rows that the query returns.

	offset(Integer offset)
	- Defines the offset for the start position of the resulting rows.

