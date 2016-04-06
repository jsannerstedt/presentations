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
* Guide for finding products (prisjakt)
* Global market - differentiate products by location and language
* Possibly online store
* Possibly server rendered (for seo and speed)
* Hosting unknown, iis as of today but hopefully cloud later
* ie11 and above. Mobile first

--

## Demo

--

### The view
* Life expectancy of code has gone down (remember Klever on leetspeak?)
* My conclusion rather than fighting this and spending more time on your code, is to embrace it. Make it easier to swap out code by making sure it's replaceable
* If I can re-write my app to use another view engine in less than a day, then it really doesn't matter all that much which one I choose today

--

### The idea
* Decoupling the view
* No (almost) logic in the view
* One way data flow

--

### Dependencies
* riot
* dedux
* ie11 polyfills

--

### Riot tag example
```html
<todos>
    <ul>
        <todo-item each="{ todos }" />
    </ul>
    <script>
        this.todos = [{name: 'doit'}, {name: 'profit'}];
    </script>
</todos>

<todo-item>
    <li>{ name }</li>

    <style scoped>
    li {
        color: green;
    }
    </style>
</todo-item>
```
--

### Css
* Riot supports scoped css
* Will be prefixed with tag name and injected
* Can be exported to files on build

--

### Riot tag example - continued
```javascript
// app.js
import riot from 'riot';
riot.mount('my-app', {todos: [{ name: 'doit' }, { name: 'profit'}] });
```
```html
// index.html
<my-app>
    <todos items="{ opts.todos }" />
</my-app>
```

--

### Updating the app
```javascript
// app.js
import riot from 'riot';
let todos = [{ name: 'doit' }, { name: 'profit'}]
const app = riot.mount('my-app', { todos })[0];

// later
todos = todos.concat([{name: 'redo'}]);
app.update({ todos });
```
--

### App.tag

Basic layout of the app
```html
<product-selection>
    <app-header/>

    <router/>

    <other-awesome-component/>

    <toasts/>
</product-selection>
```

--

### Views

All views are stateless, except the router. It needs to keep track of previous view, to unmount it.

```html
<router>
    <div name="content"></div>

    <script type="text/babel">
    const mountSubView = () => {
      let currentTag, currentView;
      const viewName = opts.state.route.activeView;
      if (currentView === viewName) update(currentTag, opts.state);
      if (currentTag) dispose(currentTag);
      if (viewName) {
        // currentTag = mountTag(viewName);
        // currentView = viewName;
      }
    };
    this.on('update', () => mountSubView());
    // ...
    </script>
</router>
```

--

## Then what?

--

### Store
* One store to rule them all
* Emits event when state is updated
* Exposes getState for current state

```javascript
// app.js
import riot from 'riot';
import store from './store';

// mount with current state, this will render the app
// with whatever state the store currently holds
const app = riot.mount('my-app', { state: store.getState() })[0];
store.subscribe(state => {
    app.opts.state = state;
    app.update();
});
```

--

### Actions
Functions to which you can subscribe. Emits events when they are run

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
* Store sets up subscription on action, based on naming convention.
* When the action is triggered, the store will use the result of the modifier to modify the state.
* Grouped by a key, which will be a namespace in the state.

--

### Basic sync use case

```javascript
import { combineModifiers, createActions, createStore } from 'dedux';

const modifiers = combineModifiers({
  menu: {
    initialState: () => ({ menuOpen: false }),
    toggleMenu: payload => ({ menuOpen: payload })
  }
});

const actions = createActions(Object.keys(modifiers));

const store = createStore(modifiers, actions);

store.subscribe(state => {
  // do something intersting here, like re-rendering a view
  console.log(state);
});

actions.toggleMenu(true);
// will log { menu: { menuOpen: true } }
```

--

### Reactions
* When you need a side effect, an async call, or just to chain actions, just subscribe to the action and do whatever you please.
* I usually group reactions, and set up subscriptions based on name.

--

### Basic rest operations

```javascript
// modifiers/foo.js
export default {
    getFoo: () => ({ loading: true }),
    getFooSuccess: item => ({ item, loading: false }),
    getFooError: error ({ error, loading: false })
};
```
```javascript
// reactions/foo.js
import actions from './actions';
import api from './api';
export default {
    getFoo: id => api.get(id)
    .then(actions.getFooSuccess)
    .catch(actions.getFooError)
};
```
```javascript
// index.js
// ...
store.subscribe(state => {
   console.log(state);
});
actions.getFoo(123);
// will eventually log { foo: { item: /* whatever our api returns */ }}
```

--

### Basic app flow
* View gets all data from state
* View trigger actions through event handlers
* Actions trigger modifiers and reactions
* Modifiers modify state
* (Possible reactions are run and trigger other actions)
* A state change is emitted by the store
* View gets updated
--

### Initial state
When creating a store we can set an initial state

```javascript
import actions from './actions';
import modifiers from './modifiers';
import { createStore } from 'dedux';
const store = createStore(actions, modifiers, { todos: [{ name: 'doit' }]});
```

--

### Initial state - continued
Setting initial state can be used for several things, we could:
* get state from localStorage, if we always save state we can now reload and keep our state
* get state from the server, having the client take over on load
* set some initial state for ui-tests, avoiding having to go through unnecessary steps

--

### Initial state - continued

```javascript
/* ...imports */

// will hydrate state from localStorage
// same technique would be used if we want to get some initial state from the server
const initial = JSON.parse(localStorage.getItem('state'));
const store = createStore(actions, modifiers, initial);
const app = riot.mount('my-app', { state: store.getState() })[0];

```

--

### some examples...
* Menu toggle
* Loading indicator
* Routing
* Filtering
* Debouncing updates

--

## Thank you!
