title: Frontend architecture tribe
author:
  name: Joel Sannerstedt
  url: http://jsannerstedt.github.io/
output: tribe.html
controls: false

--

# Le tribe
## 2016-04-06

--

### Background

--

### Demo


--

### Overview


--

### Store
* One store to rule them all.
* Emits event when state is updated.

--

### Updating the app
* Riot stuff…
* (riot routing) all views are stateless, except the router. It needs to keep track of previous view, to unmount it.

--

### Actions
* Functions to which you can subscribe. Emits events when they are run

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

### Examples
* Basic rest operations
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
