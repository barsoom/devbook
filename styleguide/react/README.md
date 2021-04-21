![Barsoom](http://barsoom.se/barsoom.png)

# React Styleguide

TODO: Merge this with the React stuff in [our JavaScript styleguide](/styleguide/javascript).

Also see [our top-level styleguide](/styleguide): it explains how and when to follow our styleguides, and how to change them.

This is an aspirational styleguide: Use this style for new projects. Move old projects in this direction, even if they have "broken windows", so we don't add to the burden of harder-to-maintain legacy styles.

This styleguide intentionally addresses both higher-level structure and lower-level details, based on the current needs of our team.

---


#### Use curlies around non-string attribute values.

Do:

``` jsx
<div className="foo" key={bar}></div>
```

Don't do this:

``` jsx
<div className={"foo"} key=bar></div>
```

#### Put a space before the slash (`… />`) in self-closing tags.

E.g. `<MyComponent foo={bar} />`, not `<MyComponent foo={bar}/>`.

Just as a convention.


#### Unbind events and timers when unmounting.

Any event listeners that are bound on mount should be unbound when we unmount. Timers and intervals should be cleared.

This is to avoid confusing behavior or memory leaks. Even if we think a component may never be unmounted, things can change.

jQuery's namespaced listeners can make this easier. The namespace should match the component name and be globally unique. We don't want to unbind someone else's listeners.

Example:

``` coffee
MyThing = React.createClass
  componentDidMount: ->
    $(document).on "CustomEvent.MyThing", fun
    $(document).on "keydown.MyThing", fun
    @timeout = setTimeout(fun, 1000)
    @interval = setInterval(fun, 1000)

  componentWillUnmount: ->
    $(document).off ".MyThing"
    clearTimeout(@timeout)
    clearInterval(@interval)
```


#### Use a `_render_` prefix for functions that return some piece of rendered output.

We want it to be clear which functions are part of actual rendering.

This applies to functions that return HTML or strings that are part of the rendered output.

It does not apply to functions that retrieve data that is not rendered as-is.

Do:

``` coffee
render: ->
  data = @_retrieveSomeData()

  <div>
    <label>#{@_renderName()}</label>
    #{@_renderInput()}
  </div>
```

Don't do this:

``` coffee
render: ->
  <div>
    #{@_input()}
  </div>
```
