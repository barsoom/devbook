![Barsoom](http://barsoom.se/barsoom.png)

# Deploy without downtime

We deploy without downtime when we can to avoid user frustration. It's pretty rare that we can't.

## Resources
* ["Zero downtime deploys for Rails" slideshow](http://www.slideshare.net/pedrobelo/zero-downtime-deploys-for-rails-apps)
* ["Zero downtime deploys for Rails" video](http://www.youtube.com/watch?v=R6bVTthtnZ0)

## Terminology

We call a revision/migration "safe" if it can be deployed without downtime.

The application before a deploy is the "old app".

The application after a deploy is the "new app".

## Considerations

* A migration is only safe if the old app works with the new state of the DB.

* TODO
