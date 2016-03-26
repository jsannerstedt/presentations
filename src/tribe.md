title: Frontend architecture tribe
author:
  name: Joel Sannerstedt
  url: http://jsannerstedt.github.io/
output: tribe.html
controls: false
style: ../main.css

--

# FE arch tribe
## 2016-04-06

--

### Background

--

## Demo


--

### Overview
* riot
* dedux

--

### Updating the app
* Riot stuff…
* (riot routing) all views are stateless, except the router.

--

### Store
* One store to rule them all.
* Emits event when state is updated.
It needs to keep track of previous view, to unmount it.

--

### Actions
* Functions to which you can subscribe. Emits events when they are run

```javascript
import { createActions } from 'dedux';

const actions = createActions(['firstAction', 'secondAction']);
actions.firstAction.subscribe(payload => {
  console.log(payload);
});
actions.firstAction('hello'); // will log hello
```

--

### Actions - continued

```javascript
function action() {
  const listeners = [];
  const trigger = data =>
    listeners.forEach(listener => listener(data));

  trigger.subscribe = callback => {
    listeners.push(callback);
    return () => listeners.splice(listeners.indexOf(callback), 1);
  };

  return trigger;
}
```

--

### Modifiers
* Pure functions, returning an object.
* Store sets up subscription on action, based on name.
* When the action is triggered, the store will use the result of the modifier to modify the state.
* Grouped by a key, which will be a namespace in the state.

--

### Reactions
* When you need a side effect, an async call, or just to chain actions, just subscribe to the action and do whatever you please.
* I usually group reactions, and set up subscriptions based on name.

--

### Recap
* View gets all data from state
* View trigger actions
* Actions modifies state, and triggers reactions
* Reactions can trigger other actions
* State gets updated by running corresponding modifiers
* A state change is emitted by the store
* View gets updated

--

## Examples

--

### Some helpers
* If we create actions from modifier and reaction keys
* And we auto-subscribe to actions by reaction key
* We can do something like this...

--

### Basic rest operations

```javascript

// modifiers/foo.js
export default {
    getFoo: () => ({ loading: true }),
    getFooSuccess: item => ({ item, loading: false }),
    getFooError: error ({ error, loading: false })
};

// reactions/foo.js
import actions from './actions';
import api from './api';
export default {
    getFoo: id => api.get(id)
    .then(actions.getFooSuccess)
    .catch(actions.getFooError)
};

// index.js
// ...
store.subscribe(state => {
   console.log(state);
});
actions.getFoo(123);
// will eventually log { foo: { item: /* whatever our api returns */ }}
```

--

### other examples

* Filtering
* Toggling
* Debouncing updates

--

### Routing
* Routing is a group of actions.

--

### Css
* Keep css with the tag. Then export and concat…

--

### Questions
* Returning a new array in a modifier, will re-evaluate each-binding. How to avoid reinit of underlying control?
* Readability?
