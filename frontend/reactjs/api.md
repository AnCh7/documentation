### React Top-Level API

##### Components

React components let you split the UI into independent, reusable  pieces, and think about each piece in isolation. React components can be defined by subclassing `React.Component` or `React.PureComponent`.


##### React.memo 

`React.memo` is a [higher order component](https://reactjs.org/docs/higher-order-components.html).

If your component renders the same result given the same props, you can wrap it in a call to `React.memo` for a performance boost in some cases by ==memoizing== the result. This  means that React will skip rendering the component, and reuse the last  rendered result.

