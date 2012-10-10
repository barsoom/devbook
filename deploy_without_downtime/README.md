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

Specifically:

* **Adding columns** is always safe.

* **Removing columns** is never safe.
  The old app will attempt to use the cached column name and will break things.

  Deploy removals in two steps:

  * Deploy an app that ignores the column.

    ``` ruby
    class Item < ActiveRecord::base
      def self.columns
        super.reject { |c| c.name == "description" }
      end
    end
    ```
  * Deploy the migration, as the old app will no longer have the column name cached.

* TODO


### Assets

* TODO


### Requests

Someone may post a form, follow a link etc from the old app where the resulting request is handled by the new app.

If the old and new apps don't expect the exact same parameters and paths, the user may see an exception or a 404.

We generally don't attempt to handle this as problems are rare at our scale.

If you do need to handle them, you can do so by making the new app handle old-style input for a while, using aliases, no-ops and similar.

If it's very common and important in a particular instance, perhaps the forms and controllers can be explicitly versioned like an API. We've never had to do this.
