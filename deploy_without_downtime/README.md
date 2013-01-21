![Barsoom](http://barsoom.se/barsoom.png)

# Deploy without downtime

We deploy without downtime when we can to avoid user frustration. It's pretty rare that we can't.

There's a great [talk](http://www.youtube.com/watch?v=R6bVTthtnZ0)/[slideshow](https://github.com/barsoom/devbook/tree/master/deploy_without_downtime) on this, but no convenient reference that we're aware of – hence this document.

## Terminology

We call a revision/migration "safe" if it can be deployed without downtime.

The application before a deploy is the "old app". The application after a deploy is the "new app".


## Considerations


### Database

* A migration is only safe if the old app works with the new state of the DB.

  This is because deploys will have a period where the migrations have run (in part or in whole) while the old app is still in play.

* A migration is not safe if it locks the DB for long.

* The below is for PostgreSQL, which we use. Much of it is true of any DB engine, but locking in particular will vary between engines.

Specifically:

* **Adding columns** is always safe.

* **Removing columns** is never safe.
  The old app will attempt to use the cached column name and will break things.

  Deploy removals in two steps:

  * Deploy 1: Make the app ignore the column:

    ``` ruby
    class Item < ActiveRecord::base
      def self.columns
        super.reject { |c| c.name == "description" }
      end
    end
    ```
  * Deploy 2: A migration to remove the column. The old app will no longer have the column name cached.

* **Renaming columns** is never safe.

  You can think of it as adding a duplicate column, then removing the old one.

  Deploy it in three steps:

  * Deploy 1:
    * Make the app write to both the old and the new column. Now all new records will have a value in the new column.

  * Deploy 2:
    * Migrate old records to copy the old column to the new column. Now both old and new records will have a value in the new column.
    * The app should ignore the old column (see above). Reference only the new column in code; not the old one.

  * Deploy 3:
    * Remove the old column from the DB.

  Note that if you migrate old records in the same deploy as you start writing to the new column, any records updated between the migration completing and the app server restarting will not be correct.

* **Adding tables** is always safe.

* **Removing tables** is never safe.

  Just like with removing columns, deploy them in two steps:

  * Deploy an app that doesn't use those tables at all.

  * Deploy the migration that removes those tables.

* **Creating indexes** is TODO.

* **Removing indexes** is always safe.


### Assets

* TODO


### Requests

Someone may post a form, follow a link etc from the old app where the resulting request is handled by the new app.

If the old and new apps don't expect the exact same parameters and paths, the user may see an exception or a 404.

We generally don't attempt to handle this as problems are rare at our scale.

If you do need to handle them, you can do so by making the new app handle old-style input for a while, using aliases, no-ops and similar.

If it's very common and important in a particular instance, perhaps the forms and controllers can be explicitly versioned like an API. We've never had to do this.
