# Mapping State & Actions to Props

![Leather Bound Books](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2FerldqIniuBYo8%2Fgiphy.gif&f=1&nofb=1)

## Overview
In this lesson, we'll continue working on our ToDo list and learn how we can map our state and Actions to our props. Make sure you have the first 3 parts of the ToDo list working before we begin.

## Objectives
- Attach our Actions and Reducers to components
- Perform state updates with our Actions
- Connect our components to our Redux Store

<img height="500" src="https://d33wubrfki0l68.cloudfront.net/01cc198232551a7e180f4e9e327b5ab22d9d14e7/b33f4/assets/images/reduxdataflowdiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif" alt="redux">

## Instructions
At this point, we've successfully created Reducers, Types, Actions, and linked our Store to our React app via the **react-redux** `Provider` component. Let's add the final piece to our puzzle: *utilizing* our Store and Actions.

### Linking Components to the Redux Store

Start by creating a `components` directory in your `src` directory.  In that directory, create two files: `TodoList.js` and `TodoForm.js`.

Open your `TodoList.js` and let's make a functional React component:

```jsx
const TodoList = () => {

  return (
    <div>
    
    </div>
  )
}

export default TodoList
```

Now, in order to use our Redux Store, we need to accept the information as props. Unfortunately, it's not as easy as just adding props to the function argument. We'll need to do a couple of things first:
- Map Our State to Props
- Map Our Actions to Props
- `connect` our component to Redux

Notice the wording in the above list. It's done purposefully. These are exactly what we're going to create and import in our `TodoList` component.

Start by creating a `mapStateToProps` function. It should accept state as an argument and return an empty object for now. Add it above the export statement:

```js
const mapStateToProps = (state) => {
  return {}
}
```

Add a `console.log` inside of that function above the `return`. Log `state`.

Now, create a `mapActionsToProps` function. It should accept `dispatch` as an argument. Have it return an empty object for now:

```js
const mapActionsToProps = (dispatch) => {
  return {}
}
```

![Glass Case of Emotion](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2FFSnwQf1mPhN2o%2Fgiphy.gif&f=1&nofb=1)

You may see this function called `mapDispatchToProps` in certain posts and tutorials online. The names of these functions don't matter, but what does matter is *what they return*.

Now, we need to add one final thing to finish our setup. We need to `connect` our `TodoList` component to Redux.

Import the `connect` function from `react-redux`. Use destructuring syntax for this:

```js
import { connect } from 'react-redux'
```

Let's connect our component now. Where we export our `TodoList`, invoke the `connect` function and wrap the `TodoList` in parentheses:

```js
export default connect()(TodoList)
```

Connect accepts two arguments, and the order matters here!
  1. The first argument is the function we're using to read the state from Redux. In this case: `mapStateToProps`.
  2. The second argument is any Actions that we want to connect to Redux. In this case: `mapActionsToProps`.

We do not invoke the functions. Simply passing them in will work.

Add them to your `connect` function in the export:

```js
export default connect(mapStateToProps, mapActionsToProps)(TodoList)
```

Now, accept `props` as an argument in the `TodoList` component. Your final component should look like this:

```jsx
import { connect } from 'react-redux'

const TodoList = (props) => {

  return (
    <div>
    
    </div>
  )
}

const mapStateToProps = (state) => {
  console.log(state)
  return {}
}

const mapActionsToProps = (dispatch) => {
  return {}
}

export default connect(mapStateToProps, mapActionsToProps)(TodoList)
```

Let's add this component to our `App.js`:

```jsx
import TodoList from './components/TodoList'
import './styles/App.css'

const App = () => {

  return (
    <div className="App">
      <h3>Redux Intro</h3>
      <TodoList />
    </div>
  )
}

export default App
```

Now, open your browser's Redux Devtools and take a look at the console. You should see our Redux Store being printed to the screen!

That's all well and good. However, it's of no use to us just sitting in the console. We want to actually *use* this information. In the `mapStateToProps` function, return an object with `todoState` as a key and `state.todoState` as a value:

```js
const mapStateToProps = (state) => {
  console.log(state)
  
  return {
    todoState: state.todoState
  }
}
```

Remove the console log from the `mapStateToProps` function and console log props in the `TodoList` component. Refresh the page and you will now only see the `todoState` being logged to the console.

Now let's display some todos on our page. In the `TodoList` component, `map` through `props.todos` and return an `li` with each todo:

```jsx
const TodoList = (props) => {
  console.log(props)
  
  return (
    <div>
      {props.todoState.todos.map((todo, index) => (
        <li key={index}>{todo}</li>
      ))}
    </div>
  )
}
```

One small problem: we don't have any todos in state right now so our array is empty. Let's add some!

In your `TodoReducer`, add a todo to the `todos` state:

```js
const initialState = {
  todos: ['Make Memes'],
  newTodo: ''
}
```

Now, if you refresh your page you should see a list appear. Much better!

We've successfully read information from our Redux Store!

![Loud Noises](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2F126CZqbY33wNgc%2Fgiphy.gif&f=1&nofb=1)

### Linking Actions to Our Component

In the last part, we created some Actions to use later on. That time has come!

Add the following to your `TodoForm` file we created earlier:

```jsx
const TodoForm = (props) => {

  return (
    <form>
      <input
        type="text"
        name="newTodo"
        value={props.newTodo}
        onChange={props.handleChange}
      />
      <button type="submit" onClick={props.handleSubmit}>
        Add Todo
      </button>
    </form>
  )
}

export default TodoForm
```

Import this component into the `TodoList` component:

```js
import TodoForm from './TodoForm'
```

We'll use it right above where we are mapping through the todos:

```jsx
<div>
  <TodoForm />
  {props.todoState.todos.map((todo, index) => (
    <li key={index}>{todo}</li>
  ))}
</div>
```

Let's set up a couple of functions in our `TodoList` component:

```js
const handleChange = (event) => {}

const handleSubmit = (event) => {
  event.preventDefault()
}
```

We'll use them as helper methods to handle our form.

Pass them in as props to our `TodoForm`. Also pass in `newTodo` which we'll get from our `todoState`:

```jsx
const TodoList = (props) => {
  console.log(props)

  const handleChange = (event) => {}

  const handleSubmit = (event) => {
    event.preventDefault()
  }

  return (
    <div>
      <TodoForm
        newTodo={props.todoState.newTodo}
        handleChange={handleChange}
        handleSubmit={handleSubmit}
      />
      {props.todoState.todos.map((todo, index) => (
        <li key={index}>{todo}</li>
      ))}
    </div>
  )
}
```

![Brick Killed A Guy](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2Fj9djzcMmzg8ow%2Fgiphy.gif&f=1&nofb=1)

Now, we're ready to start using our Redux Actions!

Import the Actions from `TodoActions.js`. Remember to use destructuring syntax!

```js
import { AddTodo, RemoveTodo, CreateNewTodo } from '../store/actions/TodoActions'
```

Now for the fun part: mapping these Actions to props.

In the `mapActionsToProps` function, modify your return statement to the following:

```js
const mapActionsToProps = (dispatch) => {
  return {
    addTodo: (newTodo) => dispatch(AddTodo(newTodo)),
    removeTodo: (index) => dispatch(RemoveTodo(index)),
    createTodo: (formValue) => dispatch(CreateNewTodo(formValue))
  }
}
```

Let's break that down:
- We set up object keys with what we want these functions to be called.
- We set the values to a callback function. If we don't, React has no idea that these are functions.
- We return a `dispatch` function that accepts the Action we want to perform. Dispatch is important here!

The `dispatch` function is telling Redux to perform this Action to update our state. This is how Redux can keep track of what function performed what and allows us to build modularity into our applications.

![Angry](https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fwww.reactiongifs.com%2Fwp-content%2Fuploads%2F2013%2F10%2FAnchorman-snarling.gif&f=1&nofb=1)

We can now use these functions as props. In our helper methods we defined earlier, utilize the `addTodo` and `createTodo` function:

```js
const handleChange = (event) => {
  props.createTodo(event.target.value)
}

const handleSubmit = (event) => {
  event.preventDefault()
  props.addTodo(props.todoState.newTodo)
}
```

Now, try typing in the form and submitting it. Your new todo should be added to the todos state!

You can observe these changes through the React Devtools. Here, you can see which Action Type is being utilized and if state gets updated. We can also see which state updated.

One small problem here: our form field is never being cleared out. Let's fix this in our `TodoReducer`:

```js
    case ADD_TODO:
      return { ...state, todos: [...state.todos, action.payload], newTodo: '' }
```

The field should now be cleared every time we add a new todo!

<details><summary><strong>Here's the final TodoList component:</strong></summary>
  
```js
import { connect } from 'react-redux'
import TodoForm from './TodoForm'
import {
  AddTodo,
  RemoveTodo,
  CreateNewTodo
} from '../store/actions/TodoActions'

const TodoList = (props) => {
  console.log(props)

  const handleChange = (event) => {
    props.createTodo(event.target.value)
  }

  const handleSubmit = (event) => {
    event.preventDefault()
    props.addTodo(props.todoState.newTodo)
  }

  return (
    <div>
      <TodoForm
        newTodo={props.todoState.newTodo}
        handleChange={handleChange}
        handleSubmit={handleSubmit}
      />
      {props.todoState.todos.map((todo, index) => (
        <li key={index}>{todo}</li>
      ))}
    </div>
  )
}

const mapStateToProps = (state) => {
  //   console.log(state)
  return {
    todoState: state.todoState
  }
}

const mapActionsToProps = (dispatch) => {
  return {
    addTodo: (newTodo) => dispatch(AddTodo(newTodo)),
    removeTodo: (index) => dispatch(RemoveTodo(index)),
    createTodo: (formValue) => dispatch(CreateNewTodo(formValue))
  }
}

export default connect(mapStateToProps, mapActionsToProps)(TodoList)
```

</details>

<details><summary><strong>Heres the final TodoReducer:</strong></summary>
  
```js
import { ADD_TODO, NEW_TODO, REMOVE_TODO } from '../types'

const initialState = {
  todos: ['Make Memes'],
  newTodo: ''
}

const TodoReducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return { ...state, todos: [...state.todos, action.payload], newTodo: '' }
    case NEW_TODO:
      return { ...state, newTodo: action.payload }
    case REMOVE_TODO:
      return { ...state }
    default:
      return { ...state }
  }
}

export default TodoReducer
```

</details>

![Amazing](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fi.pinimg.com%2Foriginals%2F8f%2Fd2%2Fcd%2F8fd2cda60f9669467ae76fb34b227525.gif&f=1&nofb=1)

## You Do
- Implement the removeTodo Action. **Hint**: you can perform logic in the Reducers!
- Create your own `markComplete` Action. You should be able to mark a todo as complete. You may need to change the data type of `newTodo` in order to get this to work.
- Implement a way to sort todos by completion.

## Bonus
- Create a favoriting feature for your todos. Create a separate component and connect it to your store. You'll need a new Type and Action. You should be able to remove this favorite as well.

## Recap
We finished up our ToDo list! We learned how we can map our state and our Actions to props so that we can use them throughout our application!

![yay](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2FtLQfm7dmGqxfa%2Fgiphy.gif&f=1&nofb=1)

## Resources
- [Keep a Tight Perimeter](https://youtu.be/ipsPgNEmAXI)
