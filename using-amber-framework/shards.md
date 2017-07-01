# Shards

Add to your shard.yml file:

```crystal
dependencies:
  crecto:
    github: fridgerator/crecto
    version: 0.6.0

  sqlite3:
    github: Zhomart/crystal-sqlite3
    branch: master

  amber:
    github: Amber-Crystal/amber 
    branch: master  
```

Run this command

```
crystal deps
```

Require the shards in src/yourProject.cr

```
require "sqlite3"
require "crecto"
require "amber"
```



