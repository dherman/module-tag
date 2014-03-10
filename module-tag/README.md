# The `module` tag

ES6 makes it easy to declare external dependencies with `import`, but
in order to avoid blocking rendering, HTML needs a way to import
modules only in the context of asynchronously loaded code.

The `module` tag not only makes it convenient to import modules
asynchronously, it improves the overall web programming model by
moving *all* JS development into modules. The slogan to live by is:

> **`<module>` is a better `<script>`**

In other words, all code -- even top level or bootstrapping code --
should live within modules. For convenience, top-level modules that
are only executed to kick off an application don't need to be given
names. But they still have the benefits of modules:

* they can declaratively import from other modules
* they're defined in a nested scope, making top-level IIFE
  boilerplate a thing of the past
* they're implicitly strict, eliminating some of the most egregious
  misfeatures (e.g., `with`) and interoperability hazards (e.g.,
  block-local function declarations) of legacy JS

To understand the design and how you might build apps with the
`module` tag, see the [explainer document](explainer.md).
