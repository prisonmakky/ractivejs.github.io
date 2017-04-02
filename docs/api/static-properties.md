# Static Properties

## Ractive.adaptors

`(Object<string, Object>)`

The registry of globally available [adaptors](../extend/adaptors.md).

---

## Ractive.components

`(Object<string, Function>)`

The registry of globally available [component definitions](../extend/components.md).

---

## Ractive.DEBUG

`(boolean)`

Tells Ractive if it's in debug mode or not. When set to `true`, non-fatal errors are logged. When set to `false`, non-fatal errors are suppressed. By default, this is set to `true`.

---

## Ractive.DEBUG_PROMISES

`(boolean)`

Tells Ractive to log errors thrown inside promises. When set to `true`, errors thrown in promises are logged. When set to `false`, errors inside promises are suppressed. By default, this is set to `true`.

---

## Ractive.decorators

`(Object<string, Function>)`

The registry of globally available [decorators](../extend/decorators.md).

---

## Ractive.defaults

`(Object<string, any>)`

The defaults for [initialisation options](../api/initialization-options.md) with the exception of [plugin registries](../integrations/plugins.md).

```js
// Change the default mustache delimiters to [[ ]] globally
Ractive.defaults.delimiters = [ '[[', ']]' ];

// Future instances now use [[ ]]
ractive1 = new Ractive({
    template: 'hello [[world]]'
});
```

Configurations set on the instance override the ones present in `Ractive.defaults`.

```js
Ractive.defaults.delimiters = [ '[[', ']]' ];

// Uses the delimiters specified above
new Ractive({
	template: 'hello [[world]]'
});

// Uses the delimiters specified in the init options
new Ractive({
	template: 'hello //world\\',
	delimiters: [ '//', '\\' ]
});
```

Defaults can also be specified a subclass of Ractive.

```js
var MyRactive = Ractive.extend();

MyRactive.defaults.el = document.body;
```

---

## Ractive.easing

`(Object<string, Function>)`

The global registry of [easing functions](../extend/easings.md).

The easing functions are used by the [`ractive.animate`](../api/instance-methods.md#ractive.animate) method and by [transitions](../extend/transitions.md). Four are included by default: `linear`, `easeIn`, `easeOut` and `easeInOut`.

---

## Ractive.events

`(Object<string, Function>)`

The global registry of [events](../extend/events.md).

---

## Ractive.interpolators

`(Object<string, Function>)`

A key-value hash of interpolators use by [`ractive.animate()`](../api/instance-methods.md#ractive.animate()).

---

## Ractive.length

Since `Ractive` is a function, and functions have a `length` equal to their number of declared arguments, `Ractive` has a `length` of `1`.

---

## Ractive.magic

Indicates whether or not the current environment supports [magic mode](../concepts/data-binding/magic-mode.md).

---

## Ractive.name

Like `length`, functions also have a `name`, and `Ractive`'s happens to be `"Ractive"`.

---

## Ractive.partials

`(Object<string, string|Object|Function>)`

The global registry of [partial templates](../extend/partials.md).

Like [templates](../concepts/templates/overview.md), partials are [parsed](../concepts/templates/parsing.md) at the point of use. The parsed output is cached and utilized for future use.

---

## Ractive.Promise

`(Function)`

A spec-compliant Promise implementation.

---

## Ractive.svg

`(boolean)`

Indicates whether or not the browser supports SVG.

---

## Ractive.transitions

`(Object<string, Function>)`

The global registry of [transition functions](../extend/transitions.md).

---

## Ractive.VERSION

The version of the currently loaded Ractive.