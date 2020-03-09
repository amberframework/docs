# Models

{% hint style="info" %}
This section is based on [Jennifer Docs](https://github.com/imdrasil/jennifer.cr/blob/master/docs/index.md).
{% endhint %}

## Several model examples

```ruby
class Contact < Jennifer::Model::Base
  with_timestamps
  mapping(
    id: {type: Int32, primary: true},
    name: String,
    gender: {type: String, default: "male", null: true},
    age: {type: Int32, default: 10},
    description: {type: String, null: true},
    created_at: {type: Time, null: true},
    updated_at: {type: Time, null: true}
  )

  has_many :addresses, Address
  has_many :facebook_profiles, FacebookProfile
  has_and_belongs_to_many :countries, Country
  has_and_belongs_to_many :facebook_many_profiles, FacebookProfile, join_foreign: :profile_id
  has_one :main_address, Address, {where { _main }}
  has_one :passport, Passport

  validates_inclusion :age, 13..75
  validates_length :name, minimum: 1, maximum: 15
  validates_with_method :name_check

  scope :main { where { _age > 18 } }
  scope :older { |age| where { _age >= age } }
  scope :ordered { order(name: :asc) }

  def name_check
    if @description && @description.not_nil!.size > 10
      errors.add(:description, "Too large description")
    end
  end
end

class Address < Jennifer::Model::Base
  mapping(
    id: {type: Int32, primary: true},
    main: Bool,
    street: String,
    contact_id: {type: Int32, null: true},
    details: {type: JSON::Any, null: true}
  )
  validates_format :street, /st\.|street/

  belongs_to :contact, Contact

  scope :main { where { _main } }
end

class Passport < Jennifer::Model::Base
  mapping(
    enn: {type: String, primary: true},
    contact_id: {type: Int32, null: true}
  )

  validates_with [EnnValidator]
  belongs_to :contact, Contact
end

class Profile < Jennifer::Model::Base
  mapping(
    id: {type: Int32, primary: true},
    login: String,
    contact_id: Int32?,
    type: String
  )

  belongs_to :contact, Contact
end

class FacebookProfile < Profile
  sti_mapping(
    uid: String
  )

  has_and_belongs_to_many :facebook_contacts, Contact, foreign: :profile_id
end

class TwitterProfile < Profile
  sti_mapping(
    email: String
  )
end

class Country < Jennifer::Model::Base
  mapping(
    id: {type: Int32, primary: true},
    name: String
  )

  validates_exclusion :name, ["asd", "qwe"]
  validates_uniqueness :name

  has_and_belongs_to_many :contacts, Contact
end
```

`mapping` macros stands for describing all model attributes. If a field has no extra parameter, you can specify name and type \(type in case of crystal language\): `field_name: :Type`. But you can use a tuple and provide the following parameters:

| argument | description |
| --- | --- |
| `:type` | crystal data type \(don't use question mark - for now you can use only `:null` option\) |
| `:primary` | mark field as primary key \(default is `false`\) |
| `:null` | allows field to be `nil` \(default is `false` for all fields except primary key |
| `:default` | default value which is set in creating a **new** object |
| `:getter` | if getter should be created \(default - `true`\) |
| `:setter` | if setter should be created \(default - `true`\) |

> By default, expect that all fields are defined in the model. If that is not true, you should pass `false` as the second argument and override the `::field_count` method to represent the correct field count.

It defines the following methods:

| method | args | description |
| --- | --- | --- |
| `#initialize` | Hash\(String \| Symbol, DB::Any\), NamedTuple, MySql::ResultSet | constructors |
| `::field_count` |  | number of fields |
| `::field_names` |  | all fields names |
| `#{{field_name}}` |  | getter |
| `#{{field_name}}_changed?` |  | represents if field is changed |
| `#{{field_name}}!` |  | getter with `not_nil!` if `null: true` was passed |
| `#{{field_name}}=` |  | setter |
| `::_{{field_name}}` |  | helper method for building queries |
| `#{{field_name}}_changed?` |  | shows if field was changed |
| `#changed?` |  | shows if any field was changed |
| `#primary` |  | value of primary key field |
| `::primary` |  | returns criteria for primary field \(query dsl\) |
| `::primary_field_name` |  | name of primary field |
| `::primary_field_type` |  | type of primary key |
| `#new_record?` |  | returns `true` if record has `nil` primary key \(is not stored to db\) |
| `::create` | `Hash(String \| Symbol, DB::Any)`, `NamedTuple` | creates object, stores it to db and returns it |
| `::create!` | `Hash(String \| Symbol, DB::Any)`, `NamedTuple` | creates object, stores it to db and returns it; otherwise raise exception |
| `::build` | `Hash(String \| Symbol, DB::Any), NamedTuple` | builds object |
| `::create` | `Hash(String \| Symbol, DB::Any)`, `NamedTuple` | builds object from hash and saves it to db with all callbacks |
| `::create!` | `Hash(String \| Symbol, DB::Any)`, `NamedTuple` | builds object from hash and saves it to db with callbacks or raise exception |
| `#save` |  | saves object to db; returns `true` if success and `false` elsewhere |
| `#save!` |  | saves object to db; returns `true` if success or raise exception otherwise |
| `#to_h` |  | returns hash with all attributes |
| `#to_str_h` |  | same as `#to_h` but with String keys |
| `#attribute` | `String \| Symbol` | returns attribute value by its name |
| `#attributes_hash` |  | returns `to_h` with deleted `nil` entries |
| `#changed?` |  | check if any field was changed |
| `#set_attribute` | `String \| Symbol`, `DB::Any` | sets attribute by given name |
| `#attribute` | `String \| Symbol` | returns attribute value by its name |

The model is automatically associated with a table by its underscored and pluralized class name, but a custom name can be defined using the `::table_name` method in its own body before using any relation \(`::singular_table_name` - for singular variant\).

### STI

Single table inheritance could be used in this way:

```ruby
class Profile < Jennifer::Model::Base
  mapping(
    id: {type: Int32, primary: true},
    login: String,
    contact_id: Int32?,
    type: String
  )

  belongs_to :contact, Contact
end

class FacebookProfile < Profile
  sti_mapping(
    uid: String
  )

  has_and_belongs_to_many :facebook_contacts, Contact, foreign: :profile_id
end

class TwitterProfile < Profile
  sti_mapping(
    email: String
  )
end
```

Subclass extends superclass definition with new fields and uses string field `type` to identify itself.

> Now `Profile.all` will return objects of `Profile` class, not taking into account the `type` field and will raise an exception if the superclass doesn't override `::field_count`.

### Prepared queries \(scopes\)

You can specify a prepared query statement.

```ruby
scope :query_name { where { c("some_field") > 10 } }
scope :query_with_arguments { |a, b| where { (c("f1") == a) && (c("f2").in(b) } }
```

As you can see, arguments are next:

* scope \(query\) name
* block to be executed in query contex \(any query part could be passed: join, where, having, etc.\)

These are chainable, so you could do:

```ruby
ModelName.all.where { _some_field > 1 }
         .query_with_arguments("done", [1,2])
         .order(f1: :asc).no_argument_query
```

### Relations

There are 4 types of relations: has\_many, has\_and\_belongs\_to\_many, belongs\_to and has\_one. All of them have the same semantic but generate slightly different methods.

They take the following arguments:

* relation name
* target class
* `request` - additional request \(will be used inside of where clause\) - optional
* `foreign` - name of foreign key - optional; by default it uses the singularized table name + "\_id"
* `primary` - primary field name - optional;  by default it uses the default primary field of the class.

has\_and\_belongs\_to\_many also accepts the next 2 arguments and uses regular arguments slightly in another way:

* `join_table` - join table name; by default, relation table names are in alphabetic order, joined by underscores.
* `join_foreign` - foreign key for current model \(left foreign key of join table\)
* `foreign` - used as right foreign key
* `primary` - used as primary field of current table; for now it properly works only if both models in this relation have a primary field named `id`

All relation macros provide the following methods:

* `#{{relation_name}}` - cache relation object \(or array of them\) and returns it.
* `#{{relation_name}}_reload` - reload relation and returns it.
* `#{{relation_name}}_query` - returns query which is used to get objects of this object relation entities from the db.
* `#remove_{{relation_name}}` - removes given object from relation.
* `#add_{{relation_name}}` - adds given object to relation or builds it from has and adds.

This allows for dynamically adding/removing objects to relations and automatically sets a foreign id:

```ruby
contact = Contact.all.find!
contact.add_addresses({:main => true, :street => "some street", :details => nil})

address = contact.addresses.last
contact.remove_addresses(address)
```

`belongs_to` and `has_one` add extra method `#relation_name!` which also adds assertion for `nil`.

### Validations

[accord](https://github.com/neovintage/accord) is used for validation. There are several general macros for declaring validations:

* `validates_with_method(*names)` - accepts method name/names
* `validates_inclusion(field, value)` - checks if `value` includes `@{{field}}`
* `validates_exclusion(field, value)` - checks if `value` excludes `@{{field}}`
* `validates_format(field, format)` - checks if `{{format}}` matches `@{{field}}`
* `validates_length(field, **options)` - check `@{{field}}` size; allowed options are: `:in`, `:is`, `:maximum`, `:minimum`
* `validates_uniqueness(field)` - check if `@{{field}}` is unique

If a record is not valid, it will not be saved.

### Callbacks

Macros for defining callbacks:

* `before_save`
* `after_save`
* `before_create`
* `after_create`
* `after_initialize`
* `before_destroy`

They accept method names.

### Timestamps

`with_timestamps` macros add callbacks for `created_at` and `updated_at` fields in an update.

### Destroy

To destroy an object, use `#delete` \(called without callback\) or `#destroy`. To destroy several objects by their ids, use the class method:

```ruby
ids = [1, 20, 18]
Contact.destroy(ids)
Address.delete(1)
Country.delete(1,2,3)
```

### Update

There are several ways to update an object. Some of them were mentioned in the mapping section. A few extra methods to do this:

* `#update_column(name, value)` - sets attribute directly and stores it to the db without any callback.
* `#update_columns(values)` - same for several columns.
* `#update_attributes(values)` - just set attributes.
* `#set_attribute(name, value)` - set attribute by given name.

## Query DSL

My favorite part. Jennifer allows you to build lazy evaluated queries with chaining syntax, but some methods can only be used at the end of a chain \(such as `#first` or `#pluck`\). Here is a list of all DSL methods:

### Find

An object can be retreived by id using `find` \(returns `T?`\) and `find!` \(returns `T` or raise `RecordNotFound` exception\) methods.

```ruby
Contact.find!(1)
```

### Where

`all` retrieves everything \(only at the beginning; creates empty request\).

```ruby
Contact.all
```

Specifying a where clause is really flexible. This method accepts a block which represents the where clause of the request \(or its part - you can chain several `where` methods and they will be concatenated using `AND`\).

To specify a field, use `c` method which accepts a string as the field name. As I've mentioned, after declaring model attributes, you can use their names inside of a block: `_field_name` if it is for the current table and `ModelName._field_name` if for another model. As a method name, you can specify an attribute of the model or table using underscores: `_some_model_or_table_name__field_name` - model/table name is separated from field name by "_\_". You can specify relation in space of which you want to declare condition using double_  at the beginning and block. Several examples:

```ruby
Contact.where { c("id") == 1 }
Contact.where { _id == 1 }
Contact.all.join(Address) { Contact._id == _contact_id }
Contact.all.relation(:addresses).where { __addresses { _id > 1 } }
```

Also you can use `primary` to mention primary field:

```ruby
Passport.where { primary.like("%123%") }
```

Supported operators:

| Operator | SQL variant |  |
| --- | --- |
| `==` | `=` |  |
| `!=` | `!=` |  |
| `<` | `<` |  |
| `<=` | `<=` |  |
| `>` | `>` |  |
| `>=` | `>=` |  |
| `=~` | `REGEXP`, `~` |  |
| `&` | `AND` |  |
| \` | \` | `OR` |

And operator-like methods:

| Method | SQL variant |
| --- | --- |
| `regexp` | `REGEXP`, `~` \(accepts `String`\) |
| `not_regexp` | `NOT REGEXP` |
| `like` | `LIKE` |
| `not_like` | `NOT LIKE` |
| `is` | `IS` and provided value |
| `not` | `NOT` and provided value \(or as unary operator if no one is given\) |
| `in` | `IN` |

And Postgres specific:

| Method | SQL variant |
| --- | --- |
| `contain` | `@>` |
| `contained` | `<@` |
| `overlap` | `&&` |

To specify exact sql query use `#sql` method:

```ruby
# it behaves like regular criteria
Contact.all.where { sql("age > ?",  [15]) & (_name == "Stephan") }
```

Query will be inserted "as is". Usage of `#sql` allows to use nested plain request.

**Tips**

* all regexp methods accept string representation of regexp
* use parentheses for binary operators \(`&` and `|`\)
* `nil` given to `!=` and `==` will be transformed to `IS NOT NULL` and `IS NULL`
* `is` and `not` operator accepts values: `:nil`, `nil`, `:unknown`, `true`, `false`

At the end - several examples:

```ruby
Contact.where { (_id > 40) & _name.regexp("^[a-d]") }

Address.where { _contact_id.is(nil) }
```

### Select

Raw sql for `SELECT` clause could be passed into `#select` method. This has the highest priority in forming this query part.

```ruby
Contact.all.select("COUNT(id) as count, contacts.name").group("name")
       .having { sql("COUNT(id)") > 1 }.pluck(:name)
```

### From

Also you can provide a subquery to specify the FROM clause \(but be careful with source fields during result retrieving and mapping to objects\)

```ruby
Contact.all.from("select * from contacts where id > 2")
Contacts.all.from(Contact.where { _id > 2 })
```

### Delete and Destroy

For now they both are the same - creates delete query with given conditions. `destroy` first loads objects and runs callbacks and then calls delete on each.

It can be only at the end of chain.

```ruby
Address.where { _main.not }.delete
```

### Joins

To join another table you can use `join` method passing model class or table name \(`String`\) and join type \(default is `:inner`\).

```ruby
field = "contact_id"
table = "passports"
Contact.all.join(Address) { Contact._id == _contact_id }.join(table) { c(field) == _id }
```

Query, built inside of block, will be passed to `ON` section of `JOIN`. Current context of block is joined table.

Also there are two shortcuts for left and right joins:

```ruby
Contact.all.left_join(Address) { _contacts__id == _contact_id }
Contact.all.right_join("addresses") { _contacts__id == c("contact_id") }
```

> For now Jennifer provides manual aliasing as a second argument for `#join` and automatic when using `#includes` and `#with` methods. For details check out the code.

### Relation

To join model relation \(has\_many, belongs\_to and has\_one\) pass it's name and join type:

```ruby
Contact.all.relation("addresses").relation(:passport, type: :left)
```

### Includes

To preload some relation use `includes` and pass relation name:

```ruby
Contact.all.includes("addresses")
```

If there are several includes with same table - Jennifer will auto alias tables.

### Group

```ruby
Contact.all.group("name", "id").pluck(:name, :id)
```

`#group` allows to add columns for `GROUP BY` section. If passing arguments are tuple of strings or just one string - all columns will be parsed as current table columns. If there is a need to group on joined table or using fields from several tables use next:

```ruby
Contact.all.relation("addresses").group(addresses: ["street"], contacts: ["name"])
       .pluck("addresses.street", "contacts.name")
```

Here keys should be _table names_.

### Having

```ruby
Contact.all.group("name").having { _age > 15 }
```

`#having` allows adding `HAVING` to the query. It accepts a block the same way as `#where` does.

### Exists

```ruby
Contact.where { _age > 42 }.exists? # returns true or false
```

`#exists?` check if there is any record with provided conditions. Can only be at the end of the query chain - it hits the db.

### Distinct

```ruby
Contant.all.distinct("age") # returns array of ages (Array(DB::Any | Int16 | Int8))
```

`#distinct` retrieves from db column values without repeats. Can accept column name and as an optional second parameter - table name. Can only be at the end of the query chain - it hits the db.

### Aggregation

There are 2 types of aggregation functions: ones which are working without a GROUP clause and returns single values \(e.g. `max`, `min`, `count`\), and ones working with a GROUP clause and returning an array of values.

### Max

```ruby
Contact.all.max(:name, String)
```

### Min

```ruby
Contact.all.min(:age, Int32)
```

### Avg

```ruby
Contact.all.avg(:age, Float64) # mysql specific
Contact.all.avg(:age, PG::Numeric) # Postgres specific
```

### Sum

```ruby
Contact.all.sum(:age, Float64) # mysql specific
Contact.all.sum(:age, Int64) # postgre specific
```

### Count

```ruby
Contact.all.count
```

### Group Max

```ruby
Contact.all.group(:gender).group_max(:age, Int32)
```

### Group Min

```ruby
Contact.all.group(:gender).group_min(:age, Int32)
```

### Group Avg

```ruby
Contact.all.avg(:age, Float64) # mysql specific
Contact.all.avg(:age, PG::Numeric) # Postgres specific
```

### Group Sum

```ruby
Contact.all.group(:gender).group_sum(:age, Float64) # mysql specific
Contact.all.group(:gender).group_sum(:age, Int64) # postgre specific
```

### Pagination

For now you can only specify `limit` and `offset`:

```ruby
Contact.all.limit(10).offset(10)
```

### Order

You can specifies orders to sort:

```ruby
Contact.all.order(name: :asc, id: "desc")
```

It accepts hash as well.

### Update

You can provide a hash or a named tuple with new field values:

```ruby
Contact.all.update(age: 1, name: "Wonder")
```

Will not trigers any callback.

### Eager load

As was said, Jennifer provides lazy query evaluation, so it will be performed only after trying to access an element from the collection \(any array method - it implements Enumerable\). Also you can extract the first entity via `first`. If you are sure that at least one entity in the db satisfies your query, you can call `#first!`.

To extract only some fields rather than entire objects, use `pluck`:

```ruby
Contact.all.pluck(:id, "name")
```

It returns an array of values if only one field was given, and an array of arrays if more. It accepts raw sql arguments, so be carefuo when using this when joining tables with same field names. This allows to retrieving custom data from specified select clauses.

```ruby
Contact.all.select("COUNT(id) as count, contacts.name").group("name")
       .having { sql("COUNT(id)") > 1 }.pluck(:count)
```

To load relations using same query joins needed tables \(yep you should specify join on condition by yourself again\) and specifies all needed relations in `with` \(relation name not table\).

```ruby
Contact.all.left_join(Address) { _contacts__id == _contact_id }.with(:addresses)
```

### Transaction

Transaction mechanism provides block-like syntax:

```ruby
Jennifer::Adapter.adapter.transaction do |tx|
  Contact.create({:name => "Chose", :age => 20})
end
```

If an error was raised in the transaction block, the transaction will be rolled back. To rollback a transaction, raise `DB::Rollback` exception.

A transaction will lock the connection for the current fiber, avoiding getting a new one from pool.

### Truncation

To truncate an entire table use:

```ruby
Jennifer::Adapter.adapter.truncate("contacts")
# or
Jennifer::Adapter.adapter.truncate(Contact)
```

This functionality could be useful to clear the db between test cases.

## Important restrictions

* sqlite3 has a lot of limitations so its support will not be added soon.

## Test

The fastest way to rollback all changes in the DB after a test case - transaction. So add:

```ruby
Spec.before_each do
  Jennifer::Adapter.adapter.begin_transaction
end

Spec.after_each do
  Jennifer::Adapter.adapter.rollback_transaction
end
```

to your `spec_helper.cr`. Also just regular deleting or truncation could be used but transactions provide 15x speed up \(at least for postgres; mysql gets less impact\).

{% hint style="info" %}
This functions can be safely used only under test environment.
{% endhint %}

