# Web modules

For ECMAScript modules to be ready for action on the web, their touch
points in the browser have to be specified. This document contains
documents explaining the constraints and designs of those touch
points, namely:

* **browser default loader:** the behavior of `System` in the browser. See [browser-loader](browser-loader).
* **the module tag:** the `<module>` tag is a better `<script>` tag. See [module-tag](module-tag).
