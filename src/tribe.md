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
* Guide for finding products
* Global market
* Possibly online store
* Possibly server rendered (for seo and speed)
* Hosting unknown, iis as of today but hopefully cloud later
* ie11 and above. Mobile first

--

## Demo

--

### The view
* Decoupling the view
* Life expectancy of code has gone down (remember Klever on leetspeak?)
* My conclusion rather than fighting this and spending more time on your code, is to embrace it. Make it easier to swap out code by making sure it's replaceable
* If I can re-write my app to use another view engine in less than a day, then it really doesn't matter all that much which one I choose today

--

### The idea
* No (almost) logic in the view
* One way data flow

* Declarative syntax for logic
...
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

### Riot tag example
```javascript
// app.js
import riot from 'riot';
riot.mount('my-app', {stuff: [{ name: 'kalle kula' }, { name: 'apan ola'}] });
```
```html
// index.html
<my-app>
    <ul>
        <li each="{ opts.stuff }">{ name }</li>
    </ul>
</my-app>
```

--

### Updating the app
```javascript
// app.js
import riot from 'riot';
const app = riot.mount('my-app', { state })[0];
app.update({ state });
```
--

### App.tag

* Basic layout of the app
```html
<product-selection>
    <app-header/>

    <router/>

    <app-menu/>

    <toasts/>
    <style scoped>
        /* all general styles here */
    </style>
</product-selection>
```

--
### Views

* All views are stateless, except the router. It needs to keep track of previous view, to unmount it.
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
* One store to rule them all.
* Emits event when state is updated.

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

### some examples...

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
* Returning a new array in a modifier, will re-evaluate each-binding. How to avoid re-init of underlying control?
* Readability?
