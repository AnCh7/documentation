# Tutorial: Intro to React

> References:
> https://reactjs.org/tutorial/tutorial.html


```js
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// Example usage: <ShoppingList name="Mark" />
```

ShoppingList is a **React component class**, or **React component type**. A component takes in parameters, called `props` (short for “properties”), and returns a hierarchy of views to display via the `render` method.

A special syntax called “JSX” which makes these structures easier to write. 

You can put *any* JavaScript expressions within braces inside JSX. 

Passing props is how information flows in React apps, from parents to children.

React components can have state by setting `this.state` in their constructors. 

To collect data from multiple children, or to have two child components communicate with each other, you need to declare the shared state in their parent component instead. The parent component can pass the state back down to the children by using props; this keeps the child components in sync with each other and with the parent component.

In React, **function components** are a simpler way to write components that only contain a `render` method and don’t have their own state.

```js
function Square(props) {
  return (
    <button className="square" onClick={props.onClick}>
      {props.value}
    </button>
  );
}
```

Because React cannot know our intentions, we need to specify a *key* property for each list item to differentiate each list item from its siblings. If no key is specified, React will present a warning and use the array index as a key by default.