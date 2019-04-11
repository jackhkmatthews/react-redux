# Redux Tutorials (learncode)

> notes on [this](https://www.youtube.com/playlist?list=PLoYCgNOIyGADILc3iUJzygCqC8Tt3bRXt) series of vids and [this](https://react-redux.js.org/introduction/basic-tutorial) article

# 1. How Redux works

- long to setup but pays off with complex app (use state, context and / or flux first for smaller apps)
- one big JS object, one big store
- never change object, only create new versions, store is immutable


![Screenshot_2019-04-01_at_18-dac8fe59-843f-4b03-a384-9a3f96b02685 36 27](https://user-images.githubusercontent.com/20629455/55969469-bfc81280-5c75-11e9-9ab7-762f6e7171a3.png)


# 2. Immutable JS

- when creating an object / array the variable is just a pointer:

![Screenshot_2019-04-01_at_18-2a825f1a-1535-42cf-a49b-e1e3fdeddca5 45 26](https://user-images.githubusercontent.com/20629455/55969458-bccd2200-5c75-11e9-8a36-8e9892a29e36.png)

- can use `Object.assign` to copy / extend an object
- doesn't work well with deep objects though. e.g:

    var b = Object.assign({}, {name: 'jac', things: [1, 2, 3]})
    b.things = b.things.concat(4)

# 3. Basic Redux introduction

- minimum to get use Redux is a reducer and a store
- can then subscribe to store
- can then dispatch events to store with a type and a payload
- completely un-opinionated about what you call things, apart from `payload`

# 4. Multiple Reducers

- can combine reducers to make them easier to manage
- specify which part of the state you are changing and which reducer to user e.g `user: userReducer`
- when combining reducers set initial state in reducers function definition
- always return a new state object from reducers
- can have a switch case for the same action in multiple reducers

# 5. Middleware

- like express, will intercept every `action` that comes through, can modify or cancel action (async comes into it here)
- redux dev tools are middle ware. Middle ware are passed to `createStore` function:

    export default createStore(
      rootReducer,
      window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
    );

- must call `next` like express to continue chain of events
- can also modify actions
- can also catch errors before they hit the store:

    const logger = store => next => action => {
      console.log("action fired", action);
      action.type = TOGGLE_TODO;
      next(action);
    };
    
    const error = store => next => action => {
      try {
        next(action);
      } catch (e) {
        console.log("error:", e);
      }
    };
    
    const middleware = applyMiddleware(logger, error);
    
    export default createStore(rootReducer, middleware);

# 6. Redux Async Actions

- `connect` handles subscribing. Returns a react component which can wrap your container components (ones that need access to the store for state or dispatch). `connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])`
- use thunk middleware to allow actions to return functions which include async code / side effects.

    export const addRandomTodo = () => {
      store.dispatch({ type: FETCH_TODO_START });
      return dispatch => {
        axios
          .get(`https://jsonplaceholder.typicode.com/todos/${nextTodoId}`)
          .then(req => {
            console.log(req.data);
            dispatch({
              type: FETCH_TODO_SUCCESS,
              payload: { content: req.data.title, id: ++nextTodoId }
            });
          });
      };
    };

- can use middle wear to generate actions like START and SUCCESS

# 7. Connecting React and Redux

- need to use react-redux `Provider` component and render inside that.
- need to give `store` to Provider component as prop
- then wrap components which need access to the store with `connect` also available as a decorator `@connect`
- connect can take `connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])`
-