# Database Command

The `database` or `db` command is one of the most common command that you will be operating. This
command allows you to work with the database engine of choice when the Amber application was generated.

Here is a list of the commands available:

- **create** Creates the database for the environment specified in your `config/database.yml`
- **drop**   Drops the database for the environment specified in your `config/database.yml`
- **migrate** or **m** Migrates `up` or `down` the database version
- **rollback** Rolls back your database to a specific version or to previous version
- **redo** Repeats the database version specified or lasted version
- **status** Prints the current database migration status
- **seed** Adds initial data after a database is created
- **version** Prints the database current version.

### `amber migrate` or `amber m`

```shell
amber database [COMMANDS1 COMMANDS2...]

Arguments:
  COMMANDS (Accepts multiple)  drop create migrate rollback redo status version seed
```

## Example Usage
