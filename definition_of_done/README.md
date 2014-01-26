![Barsoom](http://barsoom.se/barsoom.png)

# Definition of done

When is a feature ["done done"](http://chrislema.com/what-is-done-done/) so its ticket can be closed?

## Checklist

* Working code
* Automatic tests
  * Tests updated or added
  * All old tests pass
  * Have considered if it can be a unit test (and the test pyramid in general)
* Front-end code has been verified in browsers (for the general public: IE8+, latest Firefox, Chrome, Safari)
* Refactored (readable to others, maintainable)
  * Spend at least a few minutes, to the best of your ability
  * When in doubt, consider naming and [Sandi's rules](https://gist.github.com/henrik/4509394)
* Optimized enough (not slow when run with realistic amounts of data)
* Localized
* Documented if necessary (others can use it)
* Stakeholders have approved it
* In production (deployed, feature-toggled on, rolled out to everyone)
* Stakeholders have been informed that the feature is available
* Replaced legacy data and code has been removed
