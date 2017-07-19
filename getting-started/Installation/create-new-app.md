# Generating New Amber Application

```
Example:
  amber new ~/Code/Projects/weblog
  This generates a skeletal Amber installation in ~/Code/Projects/weblog.
  
amber new [your_app] -d [pg | mysql | sqlite] -t [slang | ecr] --deps 
cd [your_app]
```

options:`-d`defaults to pg.`-t`defaults to slang.`--deps`will run`crystal deps`for you.

### Run Locally

To test the demo app locally:

1. Create a new Postgres or Mysql database called
   `[your_app]_development`
2. Configure your database with one of the following ways.

   1. Add it in`config/database.yml`

   2. Run \(overrides the`config/database.yml)`  
      `export DATABASE_URL=postgres://[username]:[password]@localhost:5432/[your_app]_development                                      
      Should output something like:`  
      `Migrating db, current version: 0, target: [datetimestamp] OK [datetimestamp]_create_shop.sql`

3. Run the specs: `crystal spec`

4. Start your app`amber watch`

5. Then visit`http://0.0.0.0:3000/`

> Note: The`amber watch`command uses [Sentry](https://github.com/samueleaton/sentry) to watch for any changes in your source files, recompiling automatically.

If you don't want to use Sentry, you can compile and run manually:

1. Build the app`crystal build --release src/[your_app].cr`
2. Run with`./[your_app]`
3. Visit`http://0.0.0.0:3000/`



