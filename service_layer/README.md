![Barsoom](http://barsoom.se/barsoom.png)

# Service layer

**NOTE:** This is very much a work in progress!

We're experimenting with a service layer in Rails. It hasn't settled, but we're maintaining this document to try to formalize it a bit.

## Rationale

Matt Wynne sells it in his [Hexagonal Rails talk](http://www.youtube.com/watch?v=CGN4RFkhH2M).

Basically, we want to separate domain logic from the Rails controller, and treat the controller only as a web interface. Its responsibilities are taking input from parameters, cookies etc and sending output in the form of views, redirects, cookies etc, but *not* to make domain decisions.

## Additional inspiration

Jason Roelofs has a good series of articles called [Designing a Rails App](http://jasonroelofs.com/2012/05/29/designing-a-rails-app-part-1/).

We've also been greatly inspired by Robert Martin, both from his keynote, [Architecture the Lost Years](http://www.confreaks.com/videos/759-rubymidwest2011-keynote-architecture-the-lost-years), and from the article on [The Clean Architecture](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html).

## Our conventions

Example:

``` ruby
module CancelOrder
  def self.run(order, client)
    order.cancel!
    client.order_was_cancelled
  rescue Order::NotCancellableException
    client.order_was_not_cancellable
  end
end
```

Put it in `app/use_cases/cancel_order.rb`. Our app is configured to look there.

In the controller:

``` ruby
class OrdersController < ActionController::Base
  def destroy
    CancelOrder.run(some_order, self)
  end

  def order_was_cancelled
    redirect_to some_path, notice: "Awyeah!"
  end

  def order_was_not_cancellable
    redirect_to some_path, alert: "No!"
  end
end
```

One file per use case.

The class is `VerbNoun` as above.

The conventional method is `run` because it's short and we think we prefer it to `call`. We don't see that we'd ever use lambdas.

When it reads better or makes things clearer, use another method name or pass named arguments:
``` ruby
TransferMoney.run(from: a, to: b, amount: 1000)
GivePuppy.to_child(child)
```

We call the controller `client` since it doesn't *have* to be a controller. Pass it as the last argument, or as the named argument `client:`.

The callback convention is `noun_was_verbed` as above. Makes it more obvious that it's a callback.

This example use case is pretty short. We'd very likely want to extract a use case for anything longer; at this length or shorter, it may be overkill. We're still finding that limit.

## Under discussion

Some points that are particularly not-settled:

* `call` instead of `run`? Seems to be the Ruby convention for "just run this thing" except when you need to prevent use of lambdas.
* `CancelOrder.run` isn't very domainy. But `Order.cancel` easily collides with models.

## Rejected ideas and why

``` ruby
result = CancelOrder.run(some_order, self)

if result  # or maybe: if result.cancelled?
  redirect_to some_path, notice: "Awyeah!"
else
  redirect_to some_path, alert: "No!"
end
```

Returning and acting on a bool is less self-documenting and ties you to two outcomes only. Switching on the result value violates tell-don't-ask (TODO: Why in particular is that problematic?).
