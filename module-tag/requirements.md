# Requirements

## Programming model

* As convenient as `script`
* Asynchronous by default
* Predictable execution order
  * IOW should have deterministic initialization order like `defer`
* Nested scope to avoid accidental globals
* Convenient to write simple modules without creating separate files
* Convenient to bootstrap an app without creating separate file

## Backwards compatibility

* Must not break decent XSS filters

## Polyfill

* Must be possible to hide script contents in downrev browsers
  * Style with `display:none`
  * `<script type="application/x-javascript+module">`

## HTML imports

* HTML imports should be able to register modules
* Resolving JS imports should not race on whether HTML imports have been loaded
