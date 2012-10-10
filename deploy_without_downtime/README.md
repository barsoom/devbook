![Barsoom](http://barsoom.se/barsoom.png)

# Deploy without downtime

We deploy without downtime when we can to avoid user frustration. It's pretty rare that we can't.

There's a great [talk](http://www.youtube.com/watch?v=R6bVTthtnZ0)/[slideshow](https://github.com/barsoom/devbook/tree/master/deploy_without_downtime) on this, but no convenient reference that we're aware of – hence this document.

## Terminology

We call a revision/migration "safe" if it can be deployed without downtime.

The application before a deploy is the "old app". The application after a deploy is the "new app".

## Considerations

### Requests

* TODO

### Assets

* TODO

### Database

* A migration is only safe if the old app works with the new state of the DB.

* A migration is not safe if it locks the DB for long.

Specifically:

* **Adding columns** is always safe.

* **Removing columns** is never safe.
  The old app will attempt to use the cached column name and will break things.

  Deploy removals in two steps.

  * Deploy an app that ignores the column.

    ``` ruby
    class Item < ActiveRecord::base
      def self.columns
        super.reject { |c| c.name == "description" }
      end
    end
    ```
  * Deploy the migration, as the old app will not have the column name cached.

* TODO
