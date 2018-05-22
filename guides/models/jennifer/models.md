# Models

{% hint style="info" %}
This section is based on [Jennifer's Docs](https://github.com/imdrasil/jennifer.cr/blob/master/docs/index.md).
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

  validates_inclucion :age, 13..75
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

`mapping` macros stands for describing all model attributes. If field has no extra parameter, you can just specify name and type \(type in case of crystal language\): `field_name: :Type`. But you can use tuple and provide next parameters:

| argument | description |
| --- | --- |
| `:type` | crystal data type \(don't use question mark - for now you can use only `:null` option\) |
| `:primary` | mark field as primary key \(default is `false`\) |
| `:null` | allows field to be `nil` \(default is `false` for all fields except primary key |
| `:default` | default value which be set during creating **new** object |
| `:getter` | if getter should be created \(default - `true`\) |
| `:setter` | if setter should be created \(default - `true`\) |

> By default expected that all fields are defined in model. It that is not true you should to pass `false` as second argument and override `::field_count` method to represent correct field count.

It defines next methods:

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
| `#save!` |  | saves object to db; returns `true` if success or rise exception otherwise |
| `#to_h` |  | returns hash with all attributes |
| `#to_str_h` |  | same as `#to_h` but with String keys |
| `#attribute` | `String \| Symbol` | returns attribute value by it's name |
| `#attributes_hash` |  | returns `to_h` with deleted `nil` entries |
| `#changed?` |  | check if any field was changed |
| `#set_attribute` | `String \| Symbol`, `DB::Any` | sets attribute by given name |
| `#attribute` | `String \| Symbol` | returns attribute value by it's name |

Automatically model is associated with table with underscored pluralized class name, but special name can be defined using `::table_name` method in own body before using any relation \(`::singular_table_name` - for singular variant\).

### STI

Singl table inheritance could be used in next way:

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

Subclass extends superclass definition with new fields and use string fild `type` to indentify itself.

> Now `Profile.all` will return objects of `Profile` class not taking into account `type` field and will raise exception of even zomby process if superclass doesn't override `::field_count`.

### Prepared queries \(scopes\)

Also you can specify prepared query statement.

```ruby
scope :query_name { where { c("some_field") > 10 } }
scope :query_with_arguments { |a, b| where { (c("f1") == a) && (c("f2").in(b) } }
```

As you can see arguments are next:

* scope \(query\) name
* block to be executed in query contex \(any query part could be passed: join, where, having, etc.\)

Also they are chainable, so you could do:

```ruby
ModelName.all.where { _some_field > 1 }
         .query_with_arguments("done", [1,2])
         .order(f1: :asc).no_argument_query
```

### Relations

There are 4 types of relations: has\_many, has\_and\_belongs\_to\_many, belongs\_to and has\_one. All of them have same semantic but generate slightly different methods.

They takes next arguments:

* relation name
* target class
* `request` - additional request \(will be used inside of where clause\) - optional
* `foreign` - name of foreign key - optional; by default use singularized table name + "\_id"
* `primary` - primary field name - optional;  by default it uses default primary field of class.

has\_and\_belongs\_to\_many also accepts next 2 arguments and use regular arguments silghtly in another way:

* `join_table` - join table name; be default relation table names in alphabetic order joined by underscore is used
* `join_foreign` - foreign key for current model \(left foreign key of join table\)
* `foreign` - used as right foreign key
* `primary` - used as primary field of current table; for now it properly works only if both models in this relation has primary field named `id`

All relation macroses provide next methods:

* `#{{relation_name}}` - cache relation object \(or array of them\) and returns it;
* `#{{relation_name}}_reload` - reload relation and returns it;
* `#{{relation_name}}_query` - returns query which is used to get objects of this object relation entities form db.
* `#remove_{{relation_name}}` - removes given object from relation
* `#add_{{relation_name}}` - adds given object to relation or builds it from has and adds

This allows dynamically adds objects to relations with automacially settting foreign id:

```ruby
contact = Contact.all.find!
contact.add_addresses({:main => true, :street => "some street", :details => nil})

address = contact.addresses.last
contact.remove_addresses(address)
```

`belongs_to` and `has_one` add extra method `#relation_name!` which also adds assertion for `nil`.

### Validations

For validation purposes is used [accord](https://github.com/neovintage/accord) shard. Also there are several general macrosses for declaring validations:

* `validates_with_method(*names)` - accepts method name/names
* `validates_inclusion(field, value)` - checks if `value` includes `@{{field}}`
* `validates_exclusion(field, value)` - checks if `value` excludes `@{{field}}`
* `validates_format(field, format)` - checks if `{{format}}` matches `@{{field}}`
* `validates_length(field, **options)` - check `@{{field}}` size; allowed options are: `:in`, `:is`, `:maximum`, `:minimum`
* `validates_uniqueness(field)` - check if `@{{field}}` is unique

If record is not valid it will not be saved.

### Callbacks

There are next macrosses for defining callbacks:

* `before_save`
* `after_save`
* `before_create`
* `after_create`
* `after_initialize`
* `before_destroy`

They accept method names.

### Timestamps

`with_timestamps` macros adds callbacks for `created_at` and `updated_at` fields update.

### Destroy

To destroy object use `#delete` \(is called withoud callback\) or `#destroy`. To destroy several objects by their ids use class method:

```ruby
ids = [1, 20, 18]
Contact.destroy(ids)
Address.delete(1)
Country.delete(1,2,3)
```

### Update

There are several ways which allows to update object. Some of them were mentioned in mapping section. There are few extra methods to do this:

* `#update_column(name, value)` - sets directly attribute and store it to db without any callback
* `#update_columns(values)` - same for several ones
* `#update_attributes(values)` - just set attributes
* `#set_attribute(name, value)` - set attribute by given name

## Query DSL

My favorite part. Jennifer allows you to build lazy evaluated queries with chaining syntax. But some of them could be only at the and of a chain \(such as `#fisrt` or `#pluck`\). Here is a list of all dsl methods:

### Find

Object could be retrieved by id using `find` \(returns `T?`\) and `find!` \(returns `T` or raise `RecordNotFound` exception\) methods.

```ruby
Contact.find!(1)
```

### Where

`all` retrieves everything \(only at the beginning; creates empty request\)

```ruby
Contact.all
```

Specifying where clause is really flexible. Method accepts block which represents where clause of request \(or it's part - you can chain several `where` and they will be concatenated using `AND`\).

To specify field use `c` method which accepts string as field name. As I've mentioned after declaring model attributes, you can use their names inside of block: `_field_name` if it is for current table and `ModelName._field_name` if for another model. Also there you can specify attribute of some model or table using underscores: `_some_model_or_table_name__field_name` - model/table name is separated from field name by "_\_". You can specify relation in space of which you want to declare condition using double_  at the beginning and block. Several examples:

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

And postgres specific:

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

* all regexp methods accepts string representation of regexp
* use parenthesis for binary operators \(`&` and `|`\)
* `nil` given to `!=` and `==` will be transformed to `IS NOT NULL` and `IS NULL`
* `is` and `not` operator accepts next values: `:nil`, `nil`, `:unknown`, `true`, `false`

At the end - several examples:

```ruby
Contact.where { (_id > 40) & _name.regexp("^[a-d]") }

Address.where { _contact_id.is(nil) }
```

### Select

Raw sql for `SELECT` clause could be passed into `#select` method. This have highest priority during forming this query part.

```ruby
Contact.all.select("COUNT(id) as count, contacts.name").group("name")
       .having { sql("COUNT(id)") > 1 }.pluck(:name)
```

### From

Also you can provide subquery to specify FROM clause \(but be carefull with source fields during result retriving and mapping to objects\)

```ruby
Contact.all.from("select * from contacts where id > 2")
Contacts.all.from(Contact.where { _id > 2 })
```

### Delete and Destroy

For now they both are the same - creates delete query with given conditions. `destroy` firstly loads objects and run callbacks and then calls delete on each.

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

Query, built inside of block, will passed to `ON` section of `JOIN`. Current context of block is joined table.

Also there is two shortcuts for left and right joins:

```ruby
Contact.all.left_join(Address) { _contacts__id == _contact_id }
Contact.all.right_join("addresses") { _contacts__id == c("contact_id") }
```

> For now Jennifer provide manual aliasing as second argument for `#join` and automatic when using `#includes` and `#with` methods. For details check out the code.

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

`#having` allows to add `HAVING` part of query. It accepts block same way as `#where` does.

### Exists

```ruby
Contact.where { _age > 42 }.exists? # returns true or false
```

`#exists?` check is there is any record with provided conditions. Can be only at the end of query chain - it hit the db.

### Distinct

```ruby
Contant.all.distinct("age") # returns array of ages (Array(DB::Any | Int16 | Int8))
```

`#distinct` retrieves from db column values without repeats. Can accept column name and as optional second parameter - table name. Can be only as at he end of call chain - hit the db.

### Aggregation

There are 2 types of aggregation functions: ones which are orking without GROUP clause and returns single values \(e.g. `max`, `min`, `count`\) and ones, working with GROUP clause and returning array of values.

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

You can provide hash or named tuple with new field values:

```ruby
Contact.all.update(age: 1, name: "Wonder")
```

Will not trigers any callback.

### Eager load

As was said Jennifer provide lazy query evaluation so it will be performed only after trying to access to element from collection \(any array method - it implements Enumerable\). Also you can extract first entity via `first`. If you are sure that at least one entity in db satisfies you query you can call `#first!`.

To extract only some fields rather then entire objects use `pluck`:

```ruby
Contact.all.pluck(:id, "name")
```

It returns array of values if only one field was given and array of arrays if more. It accepts raw sql arguments so be care when using this with joining tables with same field names. But this allows to retrieve some custom data from specified select clause.

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

If any error was raised in block transaction will be rollbacked. To rollback transaction raise `DB::Rollback` exception.

Transaction lock connection for current fiber avoiding grepping new one from pool.

### Truncation

To truncate entire table use:

```ruby
Jennifer::Adapter.adapter.truncate("contacts")
# or
Jennifer::Adapter.adapter.truncate(Contact)
```

This functionality could be useful to clear db between test cases.

## Important restrictions

* sqlite3 has a lot of limitations so it's support will be added not soon

## Test

The fastest way to rollback all changes in DB after test case - transaction. So add:

```ruby
Spec.before_each do
  Jennifer::Adapter.adapter.begin_transaction
end

Spec.after_each do
  Jennifer::Adapter.adapter.rollback_transaction
end
```

to your `spec_helper.cr`. Also just regular deleting or truncation could be used but transaction provide 15x speed up \(at least for postgres; mysql gets less impact\).

{% hint style="info" %}
This functions can be safely used only under test environment.
{% endhint %}

