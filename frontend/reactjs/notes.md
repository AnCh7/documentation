# Functional Components vs. Class Components

A functional component is just a plain JavaScript function that returns  JSX. A class component is a JavaScript class that extends `React.Component` which has a render method. 
Inside a functional component, we are passing props as an argument of the function. In a class, you need to use `this` to refer to props.

To use state variables in a functional component, we need to use `useState` Hook, which takes an argument of initial state. 

# Convert react class-based to functional component

> References:
> https://nimblewebdeveloper.com/blog/convert-react-class-to-function-component
> https://www.digitalocean.com/community/tutorials/five-ways-to-convert-react-class-components-to-functional-components-with-react-hooks

1. Change the class to a function or use FunctionComponent
2. remove the constructor
3. Remove the render method
4. Convert all methods to functions (add const)
5. remove this.state throughout the component
6. Remove references to this
7. Set initial state with useState()
8. change this.setState() … instead, call the function that you named in the previous step to update the state…
9. replace compentDidMount with useEffect
10. replace componentDidUpdate with useEffect

# How to fetch data with React Hooks

> References:
> https://www.robinwieruch.de/react-hooks-fetch-data


```typescript
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [data, setData] = useState({ hits: [] });

  useEffect(() => {
    const fetchData = async () => {
      const result = await axios(
        'https://hn.algolia.com/api/v1/search?query=redux',
      );

      setData(result.data);
    };

    fetchData();
  }, []);

  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}

export default App;
```

# Typechecking With PropTypes

PropTypes was originally exposed as part of the React core module, and is commonly used with React components.

To run typechecking on the props for a component, you can assign the special `propTypes` property:

```js
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```

# React Native what exactly is the <> (empty) component

It's the React shortcut for `Fragment` component.
```js
import React, { Component } from 'react'
class Component extends Component {
  render() {
    return <> <ComponentA/> <ComponentB/> </>
  }
}
```
or
```js
import React, { Component, Fragment } from 'react'
class Component extends Component {
  render() {
    return <Fragment> <ComponentA/> <ComponentB/> </Fragment>
  }
}
```

# Using the Effect Hook

They let you use state and other React features without writing a class. Data fetching, setting up a subscription, and manually changing the DOM in React components are all examples of side effects.

If you’re familiar with React class lifecycle methods, you can think of useEffect Hook as componentDidMount, componentDidUpdate, and componentWillUnmount combined.

#### Effects Without Cleanup

Sometimes, we want to **run some additional code after React has updated the DOM.** Network requests, manual DOM mutations, and logging are common examples of effects that don’t require a cleanup.

```typescript
import React, { useState, useEffect } from 'react';
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {    document.title = `You clicked ${count} times`;  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

#### Effects with Cleanup

```typescript
import React, { useState, useEffect } from "react";

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return "Loading...";
  }

  return isOnline ? "Online" : "Offline";
}
```

Tips:

- Use Multiple Effects to Separate Concerns 
- Optimizing Performance by Skipping Effects

```typescript
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // Only re-run the effect if count changes
```

