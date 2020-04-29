# Straal JavaScript Style Guide

<h4>Table of contents:</h4>

<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=3 orderedList=false} -->
<!-- code_chunk_output -->

- [Constants and keys - enabled/disabled etc.](#constants-and-keys-enableddisabled-etc)
- [Checkers, validators, extractors, handlers, etc.](#checkers-validators-extractors-handlers-etc)
- [Operator functions:](#operator-functions)
- [Pipelines](#pipelines)
- [Setters and getters](#setters-and-getters)
- [Avoid if/else](#avoid-ifelse)
- [Dictionaries](#dictionaries)
- [Switches](#switches)
- [Factories](#factories)
- [Reactive streams](#reactive-streams)
- [Stream creators](#stream-creators)

<!-- /code_chunk_output -->


### Constants and keys

Boolean values describing a state of something:

```javascript
const hasFoo: Boolean
const shouldFoo: Boolean

const barDisabled: Boolean
const barEnabled: Boolean

const isDisabled: Boolean
```

### Functions with a specific role

```javascript
const fooChecker: Function
const fooValidator: Function
const fooExtractor: Function
const fooHandler: Function

const fooCallback: Function
const fooFallback: Function
```

### Operator functions

```javascript
const fooReducer = (result: Any, current: Any) => Any
const fooReduced = reduce(
   collection: Any,
   fooReducer,
   result,
)
const fooMapper = () => Any
const fooMapped = map(
   collection: Any,
   fooMapper,
)
```

For `filter()`, `some()`, `find()` and similar operations:

```javascript
const fooChecker = () => Any
const fooFiltered = filter(
   collection: Any,
   fooChecker,
)
```

### Pipelines

Functional way:
```javascript
const fooFlow = pipe(
   bar: Function,
   baz: Function,
   qux: Function,
)
```

With RxJS:
```javascript
const foo$ = bar$.pipe(
   filter(barComparator: () => Boolean),
   pluck('bar'),
)
```

### Setters and getters

Setters:
```javascript
const fooSetter = curry((obj: Object, value: Any) => {...fooObject, ...{value}})
const barSetter = (obj: Object, value: Any) => {...barObject, ...{value}}

const setFooToObj = fooSetter(fooObject: Object)
const setBarToObj = partial(barSetter: Function, barObject: Object)
```

Getters:
```javascript
const fooGetter = curry((fooObject: Object, value: Any) => fooFormatter(get(fooObject, 'value', {})))
const barGetter = (barObject: Object, value: Any) => barFormatter(get(barObject, 'value', {}))

const getFooFromObj = fooGetter(fooObject: Object)
const getBarFromObj = partial(barGetter: Function, barObject: Object)
```

### Avoid if/else

Conditional (ternary) operators:
```javascript
return foo(value)
  ? bar(value)
    ? qux()
    : quux()
  : baz()
```

A better choice for more complicated cases:
```javascript
import L from 'lodash'

const bar = (value) => value ? qux() : quux()
const sthCond = L.cond([
  [foo, bar],
  [L.stubTrue, baz]
])

return sthCond(value)
```


### Dictionaries

```javascript
const getValue (type) {
    const fooMap = {
        bar: 'bar value',
        baz: 'baz value',
        qux: 'qux value',
    }

    return fooMap[type]
}
```


### Switches

Dictionaries with getters:
```javascript
const fooGetter = curry((fooObject: Object, value: Any) => fooFormatter(get(fooObject, value, {})))
const barGetter = (barObject: Object, value: Any) => barFormatter(get(barObject, value, {}))

const makeAction = (action: Any) => {
    const bazSwitch = {
        foo: fooGetter(fooObject: Object),
        bar: partial(barGetter: Function, barObject: Object),
    }
    const actionGetter = get(bazSwitch, action, constant(null))
    return actionGetter()
}
```

### Factories

Functions returning instances with functions:

```javascript
const someFactory = function({store, fetcher}, watcher){
   const fetch = fetcher(store)
   const watch = watcher(store)
   return {
      fetch,
      watch
   }
}
const store: Object
const fooWatcher: Function
const barWatcher: Function
const someFetcher: Function
const someMiddleware = curry(someFactory)({store, fetcher: someFetcher})
const foo = someMiddleware(fooWatcher)
const bar = someMiddleware(barWatcher)
const { watch: fooWatch, fetch: fooFetch} = foo
const fooPipe = pipe(fooWatch, fooFetch)
const { watch: barWatch, fetch: barFetch} = bar
const barPipe = pipe(barWatch, barFetch)
```

### Reactive streams

Cold observables:
```javascript
const fooObserver = => Any
const fooSubscription = subscribe(
    foo$: Observable$,
    fooObserver,
)
fooSubscription.unsubscribe()
```

Hot observables:
```javascript
export const fooDispatcher$ = fromEvent(window, 'foo')
```

### Stream creators

```typescript
export const fooStreamCreator = (): Observable<TBar | never> => {
  return condition
    ? throwError(new Error('Foo error'))
    : bar$
}
```