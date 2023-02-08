> References:
> https://www.newline.co/fullstack-react/articles/redux-with-mark-erikson/
> https://redux.js.org/introduction/getting-started


The main concept behind Redux is that the entire state of an application is stored in one central location. Each component of an application can have direct access to the state of the application without having to  send `props` down to child components or using callback functions to send data back up to a parent.

Redux helps us in scenarios where multiple components want  to share some or all of the same data, but are not closely related to  one another. Redux provides a central store that can hold data from **anywhere** in the application.

A `store` is just a JavaScript object with a few methods on  it. Redux also allows individual components to be "connected" to the  store, and extract just the pieces of data that they need.


---

The whole global state of your app is stored in an object tree inside a single *store*. The only way to change the state tree is to create an *action*, an object describing what happened, and *dispatch* it to the store. To specify how state gets updated in response to an action, you write pure *reducer* functions that calculate a new state based on the old state and the action.

---

Redux is really:

- A single store containing "global" state
- Dispatching plain object actions to the store when something happens in the app
- Pure reducer functions looking at those actions and returning immutably updated state

Redux code also normally includes:

- Action creators that generate those action objects
- Middleware to enable side effects
- Thunk functions that contain sync or async logic with side effects
- Normalized state to enable looking up items by ID
- Memoized selector functions with the Reselect library for optimizing derived data
- The Redux DevTools Extension to view your action history and state changes
- TypeScript types for actions, state, and other functions

---

