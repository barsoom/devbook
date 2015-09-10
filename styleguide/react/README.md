![Barsoom](http://barsoom.se/barsoom.png)

# React Styleguide

Also see [our top-level styleguide](/styleguide): it explains how and when to follow our styleguides, and how to change them.

This is an aspirational styleguide: Use this style for new projects. Move old projects in this direction, even if they have "broken windows", so we don't add to the burden of harder-to-maintain legacy styles.

This styleguide intentionally addresses both higher-level structure and lower-level details, based on the current needs of our team.

---


#### Use `return null` to not render a component.

We've standardized on this value. `false` is also allowed, but it's not our convention.


#### Use curlies around non-string attribute values.

Do:

``` jsx
<div className="foo" key={bar}></div>
```

Don't do this:

``` jsx
<div className={"foo"} key=bar></div>
```


#### Unbind events and timers when unmounting.

Any event listeners that are bound on mount should be unbound when we unmount. Timers and intervals should be cleared.

This is to avoid confusing behavior or memory leaks. Even if we think a component may never be unmounted, things can change.

jQuery's namespaced listeners can make this easier.

Example:

``` coffee
Foo = React.createClass
  componentDidMount: ->
    $(document).on "CustomEvent.Foo", fun
    $(document).on "keydown.Foo", fun
    @timeout = setTimeout(fun, 1000)
    @interval = setInterval(fun, 1000)

  componentWillUnmount: ->
    $(document).off ".Foo"
    clearTimeout(@timeout)
    clearInterval(@interval)
```
