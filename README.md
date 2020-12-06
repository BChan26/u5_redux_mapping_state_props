# Mapping State and Actions To Props

### Requirements

- Have part 3 of the todo list working

## Objectives

- Attach our actions and reducers to components.

- Perform state updates with our actions.

- Connect our components to our redux store.

## Recap

At this point we've successfully created `reducers`, `types`, `actions`, linked our `store` to our react app via the react-redux `Provider` component. Let's add the final piece to our puzzle, utilizing our store and actions.

## Creating A Todo List

### Linking Components To The Redux Store

Start by creating a `components` folder in your `src` directory.

In that folder create two files, `TodoList.js` and `TodoForm.js`.

Open your `TodoList.js`, let's make a functional react component:

```jsx
import React from 'react'

const TodoList = () => {
  return <div></div>
}

export default TodoList
```

Now in order to use our Redux `store`, we need to accept the information as props. Unfortunately it's not as easy as just adding props to the function argument. We'll need to do a couple of things first:

- Map Our State to Props
- Map Our Actions to Props
- `Connect` our component to Redux

Notice the wording in the above list, it's done purposely. These are exactly what we're going to create and import in our `TodoList` component.

Start by creating a `mapStateToProps` function. It should accept state as an argument and return an empty object for the time being, add it above the export statement:

```js
const mapStateToProps = (state) => {
  return {}
}
```

Add a `console.log` inside of that function above the `return`. And log `state`.

Now create a `mapActionsToProps` function, it should accept `dispatch` as an argument, have it return an empty object for the time being:

```js
const mapActionsToProps = (dispatch) => {
  return {}
}
```

You may see this function called `mapDispatchToProps` in certain posts and tutorials online, the names of these functions don't matter, what does matter is what they return.

Now we need to add one final thing to finish our setup. We need to `connect` our `TodoList` component to Redux.

Import the `connect` function from `react-redux`, use desructuring syntax for this:

```js
import { connect } from 'react-redux'
```

Let's connect our component now, where we export our `TodoList` invoke the `connect` function, and wrap the `TodoList` in parentheses:

```js
export default connect()(TodoList)
```

Connect accepts two arguments, the order matters here! The first argument the function we're using to read the state from redux, in this case, `mapStateToProps`.
The second argument being any actions that we want to connect to redux, in this case `mapActionsToProps`.

We do not invoke the functions, simply passing them in will work.

Add them to your `connect` function:

```js
export default connect(mapStateToProps, mapActionsToProps)(TodoList)
```

Now accept `props` as an argument in the `TodoList` component, your final component should look like this:

```jsx
import React from 'react'
import { connect } from 'react-redux'

const TodoList = (props) => {
  return <div></div>
}

const mapStateToProps = (state) => {
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

function App() {
  return (
    <div className="App">
      <h3>Redux Intro</h3>
      <TodoList />
    </div>
  )
}

export default App
```

Now open your browser dev tools and take a look at the console, you should the our redux store being printed!

That's all fine and good, however it's of no use to use just sitting in the console, we want to actually use this information. In the `mapStateToProps` function, return an object with `todoState` as a key and `state.todoState` as a value:

```js
const mapStateToProps = (state) => {
  console.log(state)
  return {
    todoState: state.todoState
  }
}
```

Remove the console log from the `mapStateToProps` function and console log props in the `TodoList` component. Refresh the page and you should now only see the `todoState` being logged to the console.

Let's display some todos on our page, in the `TodoList` component `map` through `props.todos` and return an `li` with each todo:

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

One small problem, we don't have any todos in state right now, our array is empty, let's add some!

In your `TodoReducer` add a todo to the `todos` state:

```js
const initialState = {
  todos: ['Make Memes'],
  newTodo: ''
}
```

Now if you refresh your page you should see a list appear. Much better!

We've successfully read information from our Redux store!

### Linking Actions To Our Component

In the last part we created some actions to use later on. That time has come!

Add the following to your `TodoForm` file we created earlier:

```jsx
import React from 'react'

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

Use it right above where we are mapping through the todos:

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

Pass them in as props to our `TodoForm`, also pass in `newTodo` which we'll get from our `todoState`:

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

Now we're ready to start using our redux actions!

Import the actions from `TodoActions.js`, remember to use destructuring syntax!

```js
import {
  AddTodo,
  RemoveTodo,
  CreateNewTodo
} from '../store/actions/TodoActions'
```

Now the fun part, mapping these actions to props.
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

Break down:

- We set up object keys with what we want these functions to be called
- We set the values to a callback function, if we don't react has no idea that these are functions
- We return a `dispatch` function that accepts the action we want to perform. Dispatch is important here!

The `dispatch` function is telling redux to perform this action to update our state. This is how redux can keep track of what function performed what and allows us to build modularity into our applications.

We can now use these functions as props. In our helper methods we defined earlier, utilize the `addTodo` and `createTodo` function:

```js
const handleChange = (event) => {
  props.createTodo(event.target.value)
}

const handleSubmit = (event) => {
  event.preventDefault()
  props.addTodo()
}
```

Now try typing in the form and submitting it.
Your new todo should be added to the todos state!
You can observe these changes through the redux devtools. Here you can see which action type is being utilized and if state get's updated, which state updated.

Only one small problem here, our form field is never being cleared out, let's fix this in our `TodoReducer`:

```js
    case ADD_TODO:
      return { ...state, todos: [...state.todos, action.payload], newTodo: '' }
```

The field should now be cleared every time we add a new todo!

Here's the final `TodoList` component:

```js
import React from 'react'
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

Heres the final `TodoReducer`:

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

## You Do

- Implement the removeTodo action, **Hint**: you can perform logic in the reducers!
- Create your own `markComplete` action. You should be able to mark a todo as complete, you may need to change the data type of `newTodo` in order to get this to work.
- Implement a way to sort todos by completion.

## Bonus

Create a favoriting feature for your todos, create a separate component and connect it to your store. You'll need a new type and action. You should be able to remove this favorite as well.
