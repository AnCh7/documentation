> References:
>
> https://reactjs.org/docs/faq-ajax.html

### Where in the component lifecycle should I make an AJAX call? 

You should populate data with AJAX calls in the [`componentDidMount`](https://reactjs.org/docs/react-component.html#mounting) lifecycle method. This is so you can use `setState` to update your component when the data is retrieved.

### What does `setState` do? 

`setState()` schedules an update to a component’s `state` object. When state changes, the component responds by re-rendering.

### What is the difference between `state` and `props`? 

[`props`](https://reactjs.org/docs/components-and-props.html) (short for “properties”) and [`state`](https://reactjs.org/docs/state-and-lifecycle.html) are both plain JavaScript objects. While both hold information that  influences the output of render, they are different in one important  way: `props` get passed *to* the component (similar to function parameters) whereas `state` is managed *within* the component (similar to variables declared within a function).