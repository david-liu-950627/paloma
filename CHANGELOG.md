## 6.1.0
* https://github.com/gnclmorais/paloma/pull/11 - Thanks to @Martin-Nyaga, we’re introducing a test helpers that will allow users to test that the correct parameters were passed to Paloma.

## 6.0.0
* https://github.com/gnclmorais/paloma/pull/7 - Stop removing the Paloma hook. If you are using Paloma with an altered `_hook.html.erb` because of this issue, you might not need those changes anymore. Read more about it on [here](https://github.com/gnclmorais/paloma/pull/7) and [here](https://github.com/kbparagua/paloma/issues/101).


## 5.1.0
* https://github.com/gnclmorais/paloma/pull/1 - Replace depricated `before_filter` with `before_action`.
* Update dev dependencies, since `before_action` is no available before Rails 4.0.2.

## 5.0.1
* Bring in new maintainers to the project.
* Fix typos.

## 5.0.0
* https://github.com/kbparagua/paloma/issues/84 - Easier syntax for creating controllers.
* https://github.com/kbparagua/paloma/issues/56 - Paloma should be started manually using `Paloma.start()`.
* Introduced before callbacks.
  - https://github.com/kbparagua/paloma/issues/41
  - https://github.com/kbparagua/paloma/issues/30
* Create `controller` and `action` property for Paloma controllers.
* Remove jQuery dependency.


## 4.2.1
* https://github.com/kbparagua/paloma/issues/79 - Rendering "true" string fixed.
* Catch situations where `js` function is called more than once.

## 4.2.0
* https://github.com/kbparagua/paloma/pull/75 - Explicitly insert hook in view.

## 4.1.2
* https://github.com/kbparagua/paloma/issues/73 - Fix `js false` issue.

## 4.1.1
* https://github.com/kbparagua/paloma/pull/57 - Fix Turbolinks Issues.
* https://github.com/kbparagua/paloma/pull/55 - Fix issues when Paloma hook is not found.

## 4.1.0
* Support for Turbolinks.
* `Paloma.executeHook()` to manually run the hook from the DOM.
* Paloma hook will be appended using all `render` calls, except for calls that has the following keys `[:json, :js, :xml, :file]`.
* Restore `Paloma.engine.start()` to start processing queued request.

## 4.0.0
* https://github.com/kbparagua/paloma/issues/26 - Paloma requests are not saved on `session`.
* https://github.com/kbparagua/paloma/issues/26 - Chaining with redirect is removed.
* Routing from javascript is removed.
* Routing from Rails controller is added.
* Ability to have a controller-wide setting.
* https://github.com/kbparagua/paloma/issues/29 - Disable `console.log` and `console.warn` for non-development environments.


## 3.0.2
* Bug Fix: Converting Rails controller name to singular form.

## 3.0.1
* Bug Fix: Can't handle Rails controller with Multi-word name.
* Bug Fix: Paloma Engine is halting when a warning is encountered.
* Don't create Paloma request if rendering js, json, xml, or file.

## 3.0.0
* Rewrite Initial Release
