# ractive.unshift()

The Ractive equivalent to ```Array.unshift``` that prepends one or more elements to the array at the given keypath and triggers an update event.

> ### ractive.unshift( keypath, value )
> Returns a `Promise` (see [Promises](Promises.md)) that will resolve after the update is complete.

> > #### **keypath** *`String`*
> > The [keypath](keypaths.md) of the array to change, e.g. `list` or `order.items`.

> > #### **value**
> > The value to prepend to the beginning of the array. One or more values may be supplied.

If the given keypath does not resolve to an array, an error will be thrown.
