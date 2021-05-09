# Models

The default ORM for Amber is [**Granite**](https://github.com/amberframework/granite), a lightweight Crystal ORM with adapters for MySQL, PostgreSQL and SQLite. Although Granite is the default configuration, the Amber framework supports alternate ORMs such as [**Jennifer**](https://github.com/imdrasil/jennifer.cr) built by [Roman Kalnytskyi](https://github.com/imdrasil) is a good option.

## Granite

Granite provides a light weight ORM that is focused on taking the results of your query and mapping them to your model. It does not try to shield you from the SQL that lies underneath the mapping. Out of the box, Granite provides the several common search, read and write methods for your models such as `save`, `find_by`, `destroy`, _etc_, that map onto standard SQL INSERT, UPDATE and DELETE statements. Granite also supports basic one-to-many relationships with `belong_to` and `has_many` macros that will be familiar to developers experienced with ActiveRecord.

[Granite Documentation](https://docs.amberframework.org/granite)


## Jennifer

Jennifer is a full featured ORM with a dedicate DSL that follows the ActiveRecord pattern found in Rails closely. If you are looking for a more full featured ORM Jennifer is a great choice.

{% page-ref page="jennifer/" %}

{% hint style="info" %}
Of course you are not limited to only these options. You may have a need for a high throughput, large dataset, dynamic structured db like **MongoDB** or you need to connect to a microservice or a legacy mainframe. By not being tied to a specific model, we don't limit the possible integrations you may need.
{% endhint %}

