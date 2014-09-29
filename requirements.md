# Requirements

This document explains the requirements and design constraints for the
`module` tag.



## Programming model

### No new boilerplate

The programming model needs to be as lightweight as

```html
<script>
// app code here...
</script>
```

Otherwise it does not provide an attractive enough alternative to
`script`.

### Asynchronous

The `script` tag is synchronous by default and requires opting in to
an asynchronous execution model. Creating modules in HTML should be
automatically and exclusively asynchronous.

### Predictable execution order

Asynchronous execution introduces the possibility of race
conditions. The `defer` attribute of `script` tags enforces a
predictable execution order by sequencing initialization based on
source order of `script` tags; the `module` tag should similarly force
a deterministic order of initialization.

### Nested scope

The `script` tag puts all top-level scripts in the global scope,
making even declared variables pollute the global scope/`window`
object. The `module` tag should automatically be in a nested scope, so
that global variables' scope is restricted to the current script.

### Convenience in-the-small

Small HTML files, for writing simple tests, examples,
proofs-of-concept, etc., should not require creating separate files
with modules.

### Convenient app bootstrapping

App bootstrapping code (the "main" logic) shouldn't require separate
files.



## Backwards compatibility

### Respect decent XSS filters

The rollout should not create significant new attacks on reasonably
robust XSS filters.

### Proper processing in downrev browsers

The rollout should provide a mechanism for rolling out polyfills in
downrev browsers. It should be possible to ensure script source is
invisible and to process module contents as CDATA.



## HTML imports

### Modules in HTML imports

It should be possible for HTML import files to register modules.

### No registry races

Resolving module imports should not race on whether HTML imports have
been loaded yet.
