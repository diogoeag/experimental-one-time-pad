# PDB

bla bla

## Getting the Jar

You just nee to add the following dependency to your Maven pom.

    <dependencies>
      <dependency>
        <groupId>pdb</groupId>
        <artifactId>pdb</artifactId>
        <version>1.0</version>
      </dependency>
    </dependencies>

## Getting  started

### Establish a connection

With PDB you connect to the database of your preference using the following code.

    Properties properties = new Properties() {
        {
            setProperty(JDBC, "jdbc:postgresql://database/");
            setProperty(USERNAME, "username");
            setProperty(PASSWORD, "password");
            setProperty(ENGINE, "com.feedzai.commons.sql.abstraction.engine.impl.PostgreSqlEngine");
            setProperty(SCHEMA_POLICY, "create");
            setProperty(SCHEMA, "default");
        }
    };
    
    DatabaseEngine engine = DatabaseFactory.getConnection(properties);

Remember that you have to provide your own credentials and schema name .
This example is for PostgreSql, but PDB also supports H2, MySql, Oracle, SqlServer and others.
For a more comprehensive list of the Properties, check the javadoc documentation.

### Example Description

We present you a scenario where you have data Providers that share Streams of data with the world.
These Streams have a data Type, and they are consumed by your Modules.
The entities and its relations are modeled into SQL using PDB in the following sections.

### Table Creation

We start by creating the table to store the different data Types:

    DbEntity data_type_table = 
      new DbEntity()
      .setName("data_type")
        .addColumn("id", INT, UNIQUE, NOT_NULL)
        .addColumn("code", STRING, UNIQUE, NOT_NULL)
        .addColumn("description", CLOB)
        .setPkFields("id");

A table is represented with a DbEntity and its properties can be defined with methods:

    .setName(String name)
    - Select the name for this table.
    
    .addColumn(String name, DbColumnType type, boolean autoincrement, DbColumnProperties... constraints)
    - Create a column with a given name and type.
    - Additionaly you can had autoincrement behaviour and define some extra constraints.
    
    .setPkFields(String... columns)
    - Define which columns are part of the primary key.

To create the data_type_table you call addEntity method on the previously created database engine.
Depending on the policy you choose existing tables may be droped before creating.

    engine.addEntity(data_type_table);

Lets now create the Providers and Streams tables:

    DbEntity provider_table = 
      new DbEntity()
      .setName("provider")
        .addColumn("id", INT, true, UNIQUE, NOT_NULL)
        .addColumn("uri", STRING, UNIQUE, NOT_NULL)
        .addColumn("certified", BOOLEAN, NOT_NULL)
        .addColumn("description", CLOB)
        .setPkFields("id");
    
    engine.addEntity(provider_table);

    DbEntity stream_table = 
      new DbEntity()
      .setName("stream")
        .addColumn("id", INT, true, UNIQUE, NOT_NULL)
        .addColumn("provider_id", INT, NOT_NULL)
        .addColumn("data_type_id", INT, NOT_NULL)
        .addColumn("description", CLOB)
        .setPkFields("id")
        .addFk(
          new DbFk()
            .addColumn("provider_id")
            .setForeignTable("provider")
            .addForeignColumn("id"),
          new DbFk()
            .addColumn("data_type_id")
            .setForeignTable("data_type")
            .addForeignColumn("id"));
    
    engine.addEntity(stream_table);

You might have noticed that this stream_table has some foreign keys, which we created with addFK.
This method receives a list of the foreign keys constraints.
A foreign key is created with DbFk, and it is defined using methods:

    .addColumn(String... columns)
    - Define which columns will be part of this constraint.
    
    .setForeignTable(String foreignTable)
    - Define the foreign table we are refering to.
    
    .addForeignColumn(String... foreigmColumns)
    - Selects the affected columns in the foreign table.

The rest of the example case is created with the following code:

    DbEntity module_table = 
      new DbEntity()
      .setName("module")
      .addColumn("name", STRING, UNIQUE, NOT_NULL);
    
    engine.addEntity(module_table);
    
    DbEntity stream_to_module_table = 
      new DbEntity()
      .setName("stream_to_module")
        .addColumn("name", STRING, NOT_NULL)
        .addColumn("stream_id", INT, NOT_NULL)
        .addColumn("active", BOOLEAN, NOT_NULL)
        .setPkFields("name", "stream_id")
        .addFk(
          new DbFk()
            .addColumn("name")
            .setForeignTable("module")
            .addForeignColumn("name"),
          new DbFk()
            .addColumn("stream_id")
            .setForeignTable("stream")
            .addForeignColumn("id"));
    
    engine.addEntity(stream_to_module_table);

### Inserting Entries

Now that we have the structure of the database in place, lets populate it with some data.
EntityEntry its our representation of an entry that we want to add to the database.

    EntityEntry data_type_entry =
      new EntityEntry()
        .set("id", 1)
        .set("code", "INT16")
        .set("description", "The type of INT you always want!");

    .set(String name, Object value)
    - Define the value that will be assigned to a given column.

The values for each column were defined using the set method.
A new entry for the database is persisted with engine's method persist.

    engine.persist("data_type", data_type_entry, false);

    .persist(String tableName, EntityEntry entity, boolean autoincrement)
    - Select the table in which the new entity will be inserted.
    - If the affected table has an autoincrement column you might want to activate the autoincrement flag.
    - In case that the autoincrement behaviour is active, this method returns the generated key.

If you want to use the autoincrement behavior, you should not define the value for the affected column and activate the autoincrement flag.

    EntityEntry provider_entry =
      new EntityEntry()
        .set("uri", "from.some.where")
        .set("certified", true);
    
    long generatedKey = engine.persist("provider", provider_entry, true);

### Removing and Updating Entries

Now you want to the data or simply erase it. Lets see how this is done.

    engine.executeUpdate(
      update(table("stream"))
        .set(
          eq(column("cd"), k("Double")),
          eq(column("description", k("Double precision floatinf point number")))
        .where(eq(column(id), k(1))));

Expressions that produce changes to the database are executed with engine's executeUpdate method.
There are some defined static methods that allow you to create SQL queries.
Update is one of them.
In this section we describe queries that make changes to the database, while in the following section selection queries will be present in detail.

    update()
    - bla bla
    
    .set()
    - bla bla
    
    .where()
    - bla bla

deleting my cenas

    engine.executeUpdate(
      delete(table("stream")));
      
    engine.executeUpdate(
      delete(table("stream"
        .where(eq(column(id), k(1))));

    delete
    - bla bla
    
    .where()
    - bla bla
    
If what we seek is to delete all table entries at once, it is recommended to use the truncate query.

    engine.executeUpdate(
      truncate(table("stream")));

    truncate(Expression table)
    - Creates a truncate query that will affect the table referred by the passing expression.

### Queries

bla bla

  Expression query =
  	select(all())
		.from(table("streams"));

    List<Map<String, ResultColumn>> results = engine.query(query);
    
    for(Map<String, ResultColumn> result : results) {
      System.out.println(result.get("id").toInt() + ": " + result.get("description").toString());
    }

bla bla

    results = engine.query(
      select(all())
      .from(table("streams")));

bla bla

    results = engine.query(
      select(all())
      .from(table("streams"))
      .where(eq(column("data_type_id"), k(4))));

bla bla

    results = engine.query(
      select(all())
      .from(table("streams"))
      .where(
        and(
          eq(column("data_type_id"), k(4))),
          eq(column("provider_id"), k(1))))));

bla bla

    results = engine.query(
      select(all())
      .from(table("streams"))
      .where(
        and(
          eq(column("data_type_id"), k(4))),
          eq(column("provider_id"), k(1))))));

bla bla

    results = engine.query(
      select(column("data_type_id"), count(column("id")).alias("count")))
      .from(table("streams"))
      .where(eq(column("data_type_id"), k(4)))
      .groupby(column("data_type_id"))
      .orderby(asc()));

bla bla

select
distinct
from
where
andWhere
groupby
orderby
having
limit
offset

all
column
k

table

eq
neq

like

lt
lteq
gt
gteq

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

## Documentation

javadoc link

## License

Apache V2
http://www.apache.org/licenses/LICENSE-2.0
