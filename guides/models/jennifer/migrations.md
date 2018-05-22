# Migrations

{% hint style="info" %}
This section is based on [Jennifer's Docs](https://github.com/imdrasil/jennifer.cr/blob/master/docs/index.md).
{% endhint %}

To generate a migration run `crystal sam.cr generate:migration your_migration_name`

## Migration DSL

Generator will create template file for you with next name pattern **timestamp\_migration\_name.cr**. Empty file looks like this:

```ruby
class YourCamelcasedMigrationName20170119011451314 < Jennifer::Migration::Base
  def up
  end

  def down
  end
end
```

`up`method is needed for placing your db changes there, `down`- for reverting your changes back.

Example for creating table:

```ruby
create_table(:addresses) do |t|
  t.reference :contact # creates field contact_id with Int type and allows null values
  t.string :street, {:size => 20, :sql_type => "char"} # creates string field with CHAR(20) db type
  t.bool :main, {:default => false} # sets false as default value
end
```

## Data Types and Mappings

| internal alias | PostgreSQL | MySql | Crystal type |
| :--- | :--- | :--- | :--- |
| `#integer` | `int` | `int` | `Int32` |
| `#string` | `varchar(254)` | `varchar(254)` | `String` |
| `#bool` | `boolean` | `bool` | `Bool` |
| `#char` | `char` | - | `String` |
| `#float` | `real` | `float` | `Float32` |
| `#double` | `double precision` | `double` | `Float64` |
| `#short` | `smallint` | `smallint` | `Int16` |
| `#timestamp` | `timestamp` | `timestamp` | `Time` |
| `#date_time` | `datetime` | `datetime` | `Time` |
| `#blob` | `blob` | `blob` | `Bytes` |
| `#var_string` | `varchar(254)` | `varstring` | `String` |
| `#json` | `json` | `json` | `JSON::Any` |
| `#enum` | `enum` | `enum` | `String` |

Also if you use PostgreSQL array types are available a well: `Array(Int32)`, `Array(Char)`, `Array(Float32)`, `Array(Float64)`,`Array(Int16)`, `Array(Int32)`, `Array(Int64)`, `Array(String)`.

All of them accepts additional options:

* `:sql_type`- gets exact \(except size\) field type;
* `:null`- represent nullable if field \(by default is`false`for all types and field\);
* `:primary`- marks field as primary key field \(could be several ones but this provides some bugs with query generation for such model - for now try to avoid this\).
* `:default`- default value for field
* `:auto_increment`- marks field to use auto increment \(properly works only with`Int32`fields, another crystal types have cut functionality for it\);
* `:array`- mark field to be array type \(Postgres only\)

Also there is`#field`method which allows to directly define sql type \(very suitable for enums in Postgres\).

To drop table just write

```ruby
drop_table(:addresses) # drops if exists
```

To alter existing table use next methods:

* `#change_column(name, [new_name], options)`- to change column definition; Postgres has slighly another implementation of this than mysql one - check source code for details;
* `#add_column(name, type, options)`- add new column;
* `#drop_column(name)`- drops existing column
* `#add_index(name : String, field : Symbol, type : Symbol, order : Symbol?, length : Int32?)`- adds new index \(Postgres doesn't support length parameter and only support`:unique`type\);
* `#drop_index(name : String)`- drops existing index;
* `#rename_table(new_name)`- renames table.

Also next support methods are available:

* `#table_exists?(name)`
* `#index_exists?(table, name)`
* `#column_exists?(table, name)`
* `#data_type_exists?(name)`for Postgres ENUM

Also plain SQL could be executed as well:

```ruby
execute("ALTER TABLE addresses CHANGE street st VARCHAR(20)")
```

All changes are executed one by one so you also could add data changes here \(in`up`method\) but if execution of`up`method fails -`down`method will be called and all process will stop - be ready for such behavior.

To be sure that your db is up to date before run tests of your application, add:

```text
Jennifer::Migration::Runner.migrate
```

## Enum

Now enums are supported as well but it has different implementation for adapters. For mysql is enough just write down all values:

```ruby
create_table(:contacts) do |t|
  t.enum(:gender, values: ["male", "female"])
end
```

Postgres provide much more flexible and complex behavior. Using it you need to create it firstly:

```ruby
create_enum(:gender_enum, ["male", "female"])
create_table(:contacts) do |t|
  t.string :name, {:size => 30}
  t.integer :age
  t.field :gender, :gender_enum
  t.timestamps
end
change_enum(:gender_enum, {:add_values => ["unknown"]})
change_enum(:gender_enum, {:rename_values => ["unknown", "other"]})
change_enum(:gender_enum, {:remove_values => ["other"]})
```

For more details check source code and PostgreSQL docs.

