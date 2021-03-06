# ractive.reset()


Resets the entire `ractive.data` object and updates the DOM. This differs from `ractive.set()` in the following way:

```js
ractive = new Ractive({
  // ...,
  data: { foo: 1 }
});

ractive.set({ bar: 2 });
console.log( ractive.data ); // { foo: 1, bar: 2 }

ractive.reset({ bar: 2 });
console.log( ractive.data ); // { bar: 2 }
```

> ### ractive.reset([ data ])
> Returns a `Promise` (see [Promises](Promises.md))

> > #### data *`Object`*
> > The data to reset with. Defaults to `{}`.
