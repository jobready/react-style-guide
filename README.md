# React Style Guide

*This style guide is a work in progress, and will evolve with our experience
using React components. Pull requests are welcomed.*

## Table of contents
1. [Basics](#basics)
1. [Module System](#module-system)
1. [Components](#components)
1. [Props](#props)
1. [Events](#event)
1. [Stores](#stores)
1. [Reuse](#reuse)

## Basics
- React components are written in ECMAScript 6. This is the favoured direction of React development,
so we do not use features (such as Mixins) that are not natively supported by ES6.
- Each React component (and other classes) should be contained in its own file, named in the
same manner as Rails classes (e.g. some_big_class.js would contain SomeBigClass).

## Module system
- We use the ES6 module system to require and manage code dependencies and namespacing.
- Each JS file should export exactly one class, and should do so as the default export
- Where a JS file requires code from another file, it should include it via the `import`
statement, and should assign it to a SnakeCasedVariable of the same name as the class being
required.

```js
// my_useful_component.js
export default class {
  getInitialState() {
    // ...
  }

  render () {
    // ...
  }
};

// another_file.js
import MyUsefulComponent from 'my_useful_component';

```

## Components
- A React component should be as close as possible to a pure function that deterministically
renders HTML based on the props supplied to the component without side-effects or dependency
on any state within the component.
- The sole exception to this is hyper-local state that would become cumbersome if managed
in a separate store outside the React component tree. Examples of this sort of state would
be the open/closed status of a dropdown box, the current value of a form input, or the
visible status of a tooltip.
- Do not cache any calculations or pre-rendered subcomponents in a component. React, and
Javascript in general is astonishingly fast, and these sort of optimisations are almost
always unnecessary (and sometimes even counter-productive if they undermine the React
shadow DOM's diffing algorithms).
- Under no circumstances should a React component make an AJAX call. If this is happening,
the component is doing too many things, and would violate the Single Responsibility Principle,
and should be split into separate classes each doing one thing.

## Props
- Props should be named with lowerCamelCase names.
```javascript
// Bad
<MyComponent
  ThisProp="fail"
  that_prop="also fails"
  ANOTHER_PROP="What even is this."
/>

// Good
<MyComponent
  firstProp = "such nice"
  secondProp = "very wow"
/>
```
- Props are immutable, and must never, ever, be changed by the component that received them.
- Feel free to use immutable-js to represent data.
- Where the types of the props expected to be passed to a component are known (and they should
almost always be known), use React's `propTypes` component property to allow React to check
the type of the data being passed in.

## Stores
- Most of the state data should be stored in stores external to the React component tree,
and there should be a few special components that subscribe to changes from the stores,
and then update the components below them in a declarative manner.

## Events
- Use the [events](https://www.npmjs.com/package/events) package as the global
event bus.
- Any communication between distant components in the application should be via
events (and intermediate dispatchers etc).
- For any reusable components, namespace the events being emitted with the
component name.

```javascript
// Bad
EventEmitter.emit('buttonClicked')

// Good
EventEmitter.emit('react-portal-jobs.buttonClicked')
```

## Reuse
- Don't put components or groups of components into their own repo until a clear
need for reusability has been demonstrated - i.e. that the component is actually
going to be used in more than one application.
- Reusable components and groups of components should only make the most minimal
assumptions necessary about the context which they will be used.
- Reusable components should define a clear, documented interface with which
they interract with the application that uses them. This interface should
comprise **only** props passed to the component by the application, and events
sent by the component to the global EventEmitter.
