![Barsoom](http://barsoom.se/barsoom.png)

# Deploy without downtime

We deploy without downtime when we can to avoid user frustration. It's pretty rare that we can't.

There's a great [talk](http://www.youtube.com/watch?v=R6bVTthtnZ0)/[slideshow](https://github.com/barsoom/devbook/tree/master/deploy_without_downtime) on this, but no convenient reference that we're aware of – hence this document.

## Terminology

We call a revision/migration "safe" if it can be deployed without downtime.

The application before a deploy is the "old app". The application after a deploy is the "new app".


## Considerations


### Database

**NOTE:** This applies to Heroku-style deploys where the app code is deployed first, and migrations run after. We used to do it the other way around – see Git history for those instructions.

For Rails 4, you may need [this monkeypatch](https://github.com/rails/rails/issues/12330#issuecomment-244930976) to avoid "PG::InFailedSqlTransaction" errors.

Basically, you always need to make sure any code you deploy works both before and after its migrations run.

If you have code that only works after a migration has run, you need to deploy the migration first, and then deploy the new code.

A migration is not safe if it locks the DB for long. The below is for PostgreSQL, which we use. Much of it is true of any DB engine, but locking in particular will vary between engines.

Specifically:

* **Adding columns** is safe for columns that are allowed to be null, or for small tables. On Postgres 10 or earlier, it's not safe on big tables if the column has a default value.

  NOT NULL columns on big tables [will lock the table for a long time](http://stackoverflow.com/a/19527999/6962).
  
  Columns with default values on big tables [will rewrite the whole table and its indexes](https://stackoverflow.com/q/19525083/6962).
    
  So do them in multiple steps (not necessarily multiple deploys):
  
  * Step 1: Add the column as a NULLable column without a default.
  * Step 2: Set the default value in batches, some records at a time.
  * Step 3: Make the column NOT NULL with a default. (Or just add a default, keeping it NULLable.)

* **Removing columns** is never safe.
  The old app will attempt to use the cached column name and will break things.

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

  * Deploy 2: Make the app ignore the column, and remove it from DB.

    * Ignore it like this:

      ``` ruby
      class Item < ActiveRecord::base
        def self.columns
          super.reject { |c| c.name == "description" }
        end

        # Or, if the project includes this convenience method (our projects should):
        ignore_column :description

        # … the rest of the class
      end
      ```

      Put that method **at the very top of the class** or you risk errors like "undefined method `type' for nil:NilClass".

      If the model uses STI, make sure to put this ignore in the base class, not one of the subclasses.

    * Include a migration to remove the column.

      In a big table, you should drop each index touching the column with `DROP INDEX CONCURRENTLY` (TODO: provide Ruby code for this) before dropping the column itself, to avoid locking issues.

  * Deploy 3: Remove the code that ignored the column.

  If you get "PG::InFailedSqlTransaction" errors, you may be on Rails 4 and need [this monkeypatch](https://github.com/rails/rails/issues/12330#issuecomment-244930976).

* **Renaming columns** is never safe.

  You can think of it as adding a duplicate column, then removing the old one.

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

* **Adding tables** is always safe.

* **Changing column types** is probably not safe. One case is safe though - changing a :string to :text (internally VARCHAR(255) to TEXT). This is because they are essentially compatible, and :text can hold more data than :string. But the other way may truncate data.

* **Changing column NULL** is not safe. E.g. `change_column_null :foos, :bar_id, false`. It causes a read-and-write lock on the entire table which may cause downtime for large tables that are in active use. [Read more.](http://stackoverflow.com/q/42070628/6962)

* **Removing tables** is never safe.

  Just like with removing columns, deploy them in two steps:

  * Deploy an app that doesn't use those tables at all.

  * Deploy the migration that removes those tables.

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

* **Creating indexes** [should be done concurrently](https://robots.thoughtbot.com/how-to-create-postgres-indexes-concurrently-in).

* **Removing indexes** is always safe.


### Assets

* TODO


### Requests

Someone may post a form, follow a link etc from the old app where the resulting request is handled by the new app.

If the old and new apps don't expect the exact same parameters and paths, the user may see an exception or a 404.

We generally don't attempt to handle this as problems are rare at our scale.

If you do need to handle them, you can do so by making the new app handle old-style input for a while, using aliases, no-ops and similar.

If it's very common and important in a particular instance, perhaps the forms and controllers can be explicitly versioned like an API. We've never had to do this.
