> References:
> https://reactjs.org/docs/hello-world.html



React [separates *concerns*](https://en.wikipedia.org/wiki/Separation_of_concerns) with loosely coupled units called “components” that contain both. 

---

Elements are the smallest building blocks of React apps.

Unlike browser DOM elements, React elements are plain objects, and  are cheap to create. React DOM takes care of updating the DOM to match  the React elements.

React DOM compares the element and its children to the previous one,  and only applies the DOM updates necessary to bring the DOM to the  desired state.

---

Components let you split the UI into independent, reusable pieces, and think about each piece in isolation. 

Conceptually, components are like JavaScript functions. They accept  arbitrary inputs (called “props”) and return React elements describing  what should appear on the screen.

---

The `componentDidMount()` method runs after the component output has been rendered to the DOM. 

We can tear down the component in the `componentWillUnmount()` lifecycle method.

Using State Correctly:

- use `setState()`
- use a second form of `setState()`  that accepts a function rather than an object. That function will  receive the previous state as the first argument, and the props at the  time the update is applied as the second argument
- When you call `setState()`, React merges the object you provide into the current state. Update them independently with separate `setState()` calls.

---

Keys only make sense in the context of the surrounding array.

For example, if you [extract](https://reactjs.org/docs/components-and-props.html#extracting-components) a `ListItem` component, you should keep the key on the `<ListItem />` elements in the array rather than on the `<li>` element in the `ListItem` itself.

---

An input form element whose value is controlled by React in this way is called a “controlled component”.

---

Often, several components need to reflect the same changing data. We  recommend lifting the shared state up to their closest common ancestor.

---

React has a powerful composition model, and we recommend using  composition instead of inheritance to reuse code between components.