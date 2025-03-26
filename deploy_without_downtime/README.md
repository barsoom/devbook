# Deploy without downtime

We deploy without downtime when we can to avoid user frustration. It's pretty rare that we can't.

There's a great [talk](http://www.youtube.com/watch?v=R6bVTthtnZ0)/[slideshow](https://github.com/barsoom/devbook/tree/master/deploy_without_downtime) on this, but no convenient reference that we're aware of – hence this document.

## Terminology

We call a revision/migration "safe" if it can be deployed without downtime.

The application before a deploy is the "old app". The application after a deploy is the "new app".


## Considerations


### Database

**NOTE: IMPORTANT!**

The instructions below apply to Heroku-style deploys where the app code is deployed first, and migrations run after. We used to do it the other way around – see Git history for those instructions.

But as of 2021-03 (well, long before then) we typically run migrations first and restart the app code later. So TODO, update the instructions below!

**END OF NOTE**

Basically, you always need to make sure any code you deploy works both before and after its migrations run.

If you have code that only works after a migration has run, you need to deploy the migration first, and then deploy the new code.

A migration is not safe if it locks the DB for long. The below is for PostgreSQL, which we use. Much of it is true of any DB engine, but locking in particular will vary between engines.

**However**, all DDL statements that take an exclusive lock come with a risk even if the operation under the lock is very fast, since:
* the exclusive lock must wait for all ongoing work to finish before it can be acquired
* once someone is attempting to acquire an exclusive lock, all new incoming work must wait for it to be released

Therefore, under heavy load even a normally fast operation may cascade into a traffic jam around the affected table, if the initial wait to begin the DDL takes longer than expected. For extra safety, you can consider setting the timeout to a low value; it's better to abort a migration than to grind the database to a halt: `SET LOCAL lock_timeout = '1s';`.

### Adding columns

In certain circumstances adding a column can cause a physical rewrite of the entire table and its indexes. An exclusive lock is taken on the table for this operation. Consider the following to determine whether your change is safe:

* `NULL`, no default
  * Safe.
* Small table
  * Safe if the rewrite finishes quickly enough. You can use `SELECT pg_size_pretty(pg_total_relation_size('tablename'))` to make your decision.
* Non-volatile default
  * Safe since Postgres 11. The default value is stored in the table metadata instead of physically updating the rows.
    * `CURRENT_TIMESTAMP` is a stable function, so it is also safe.
    * See [Function Volatility Categories](https://www.postgresql.org/docs/current/xfunc-volatility.html) for more (it's not hugely helpful in this context though).
* ❗ `NULL`, volatile default
  * Safe **if the default is set separately**, i.e. first `ALTER TABLE ADD COLUMN`, then `ALTER TABLE ALTER COLUMN SET DEFAULT`.
    * Setting the default in the same statement as adding a column **will** trigger a table rewrite; yes, even on adding a `NULL` column.
    * Setting the default separately will not touch existing rows, leaving them as `NULL`.
* ❗ Anything else
  * Unsafe. Try to find a way to do a staged change, for example:
    1. Create the column as `NULL` without a default.
    2. Fill in the data in batches to avoid updating the entire table in one transaction. Remember to disable transactions (`disable_ddl_transaction!`) if this is done in a migration, or do it outside of a migration.
    3. Set the desired nullability and default on the column.

  Below, example of a safe migration for one of our largest tables, `images` with around 6M rows.
  ```
  == 20210527200847 AddOwnerIdAndOwnerTypeToImages: migrating ===================
  -- add_column(:images, :owner_type, :string)
     -> 0.0128s
  -- add_column(:images, :owner_id, :integer)
     -> 0.0440s
  == 20210527200847 AddOwnerIdAndOwnerTypeToImages: migrated (0.0571s) ==========
  ```

### Removing columns

* **Removing columns** is not safe for *tables* that are in use, unless the old (currently running) app ignores the column.

  The old app will attempt to use the cached column name and will break things.

  (If the *table* is not in use, with no queries running against it, you can remove the *column* in a single step.)

  Deploy removals in multiple steps:

  * Make sure the column is not actually in use by any code.

  * (Optional) Deploy 1: Make the column nullable if it isn't already (being aware that [it's dangerous on big tables](http://stackoverflow.com/q/42070628/6962):

    ```ruby
    class MyMigration < ActiveRecord::Migration
      def change
        change_column_null :items, :description, true
      end
    end
    ```

    This is so you can safely ignore the column without breaking the creation of new records.

  * Deploy 2: Make the app ignore the column.

    * Ignore it like this:

      ``` ruby
      class Item < ActiveRecord::base
        self.ignored_columns = [ :description ]
        # … the rest of the class
      end
      ```

      Put that method call **at the very top of the class** or you risk errors like "undefined method `type' for nil:NilClass".

      If the model uses STI, make sure to do this in the base class, not one of the subclasses.
  * Deploy 3: Remove it from DB.
    
    * Include a migration to remove the column.

      In a big table, you should drop each index touching the column with `DROP INDEX CONCURRENTLY` (Example: `remove_index :companies, :bookie_api_key, name: "index_companies_on_bookie_api_key", unique: true, algorithm: :concurrently`) before dropping the column itself, to avoid locking issues. This requires the migration class to run the class method `disable_ddl_transaction!` first.

  * Deploy 4: Remove the code that ignored the column.

### Renaming columns

* **Renaming columns** is not safe for *tables* that are in use, unless the old (currently running) app ignores the column.

  Add a duplicate column with the new name, then remove the old one safely.

  (If the *table* is not in use, with no queries running against it, you can rename the *column* in a single step.)

  Deploy it in these steps:

  * Deploy 1:
    * TODO: Next time, try using a DB-level trigger instead of doing it app-level. Then update this document!
    * Add the new column.

  * Deploy 2:
    * Make the app write to both the old and the new column. Now all new records will have a value in the new column.
    * Migrate old records to copy the old column to the new column. Now both old and new records will have a value in the new column.

  * Deploy 3:
    * Ignore the old column (see above) and change all code to reference only the new column.
    * If you're daring, remove the old column. We usually leave it around for a little while until we've run some queries to verify all data is copied.
    * NOTE: Rails will let you ignore the old column and still read it with `read_attribute`/`[]` in one and the same commit/deploy.

  * Deploy 4: Remove the code that ignored the column.

### Adding tables

* **Adding tables** is always safe.

### Changing column types

* **Changing column types** is probably not safe. One case is safe though - changing a :string to :text (internally VARCHAR(255) to TEXT). This is because they are essentially compatible, and :text can hold more data than :string. But the other way may truncate data.

### Changing column NULL

* **Changing column NULL** is not safe. E.g. `change_column_null :foos, :bar_id, false`. It causes a read-and-write lock on the entire table which may cause downtime for large tables that are in active use. [Read more.](http://stackoverflow.com/q/42070628/6962)

### Changing a column default

* **Changing column default** [is safe](https://www.enterprisedb.com/blog/adding-new-table-columns-default-values-postgresql-11) since Postgres 11, even with nullable columns.

### Removing tables

* **Removing tables** is never safe.

  Just like with removing columns, deploy them in two steps:

  * Deploy an app that doesn't use those tables at all.

  * Deploy the migration that removes those tables.

### Renaming tables

* **Renaming tables** is never safe.

  You can do it much like renaming columns. Write to both tables, then migrate old records, then remove the old table.

  This may be tricky if the table is edited from a lot of places through a big API (i.e. if your model does not encapsulate Active Record).

  TODO: Explore using database-level triggers for this.

  The easiest solution is to first add the new table, then make sure the app doesn't update the source table (by disabling features and making it raise on changes in prod), then copy the data once that is in effect, then enable the feature against the new table.

  If you can't disable the feature, another more painful solution is to write to both tables, something like:

  ``` ruby
  x = OldRecord.create(params)
  # NOTE: id is a protected attribute.
  NewRecord.create(params.merge(id: x.id), without_protection: true)
  # NOTE: Postgres doesn't change the id autoincrement automatically.
  NewRecord.connection.select_value("SELECT setval('new_records_id_seq', (SELECT MAX(id) FROM new_records));")
  ```

  And you may migrate old values something like:

  ``` ruby
  first_new_id = NewRecord.minimum(:id) || 999_999_999
  insert_sql("INSERT INTO new_records (SELECT * FROM old_records WHERE id < #{first_new_id})")
  ```

### Creating indexes

* **Creating indexes** [should be done concurrently](https://robots.thoughtbot.com/how-to-create-postgres-indexes-concurrently-in).

### Removing indexes

* **Removing indexes** is always safe.


### Assets

* TODO


### Requests

Someone may post a form, follow a link etc from the old app where the resulting request is handled by the new app.

If the old and new apps don't expect the exact same parameters and paths, the user may see an exception or a 404.

We generally don't attempt to handle this as problems are rare at our scale.

If you do need to handle them, you can do so by making the new app handle old-style input for a while, using aliases, no-ops and similar.

If it's very common and important in a particular instance, perhaps the forms and controllers can be explicitly versioned like an API. We've never had to do this.
