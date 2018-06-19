# Notes on Redux

### Reducers

* Must be pure functions

## Testing

### Preventing mutations

Use [deep-freeze](https://github.com/substack/deep-freeze) to test that
objects are not being mutated.

#### Arrays

```javascript
const addCounter = (list) => {
  // bad
  list.append(0);
  return list;

  // good
  return [...list, 0];

  // good
  return list.concat([0]);
}

const incrementCounter = (list, index) => {
  // bad
  list[index]++;
  return list;

  // good
  return list
    .slice(0, index)
    .concat([list[index] + 1])
    .concat(list.slice(index + 1));

  // good
  return [
    ...list.slice(0, index),
    list[index] + 1,
    ...list.slice(index + 1)
  ];
}

const testAddCounter = () => {
  const listBefore = [];
  const listAfter = [0];

  deepFreeze(listBefore);

  expect(
    addCounter(listBefore)
  ).toEqual(listAfter);
}

const testIncrementCounter = () => {
  const listBefore = [0,1,2];
  const listAfter = [0,2,2];

  deepFreeze(listBefore);

  expect(
    addCounter(listBefore)
  ).toEqual(listAfter);
}
```

#### Objects

```javascript
const toggleTodo = (todo) => {
  // bad
  todo.completed = !todo.completed;
  return todo;

  // good (es7)
  return {
    ...todo,
    completed: !todo.completed
  };

  // good (es6)
  return Object.assign({}, todo, {
    completed: !todo.completed
  });
};

const testToggleTodo = () => {
  const todoBefore = {
    id: 0,
    text: 'Learn Redux',
    completed: false
  };
  const todoAfter = {
    id: 0,
    text: 'Learn Redux',
    completed: true
  };

  deepFreeze(todoBefore);

  expect(
    toggleTodo(todoBefore)
  ).toEqual(todoAfter);
};
```

### Reducers

Handle undefined `state`.
```javascript
// good
function counter(state, action) {
  if (typeof state == 'undefined') {
    return 0;
  }
}

// good
const counter = (state = 0, action) => {
  //... can safely ignore `undefined` state
}

expect(
  counter(undefined, { type: 'INCREMENT' })
).toEqual(0)
```

## Reducer Composition

### Objects

```javascript
const todos = (state = [], action) => {
  //... do the thing
}
const visibilityFilter = (state = 'ALL', action) => {
  //... do the thing
}

// without combineReducers
const todoApp = (state = {}, action) => {
  return {
    todos: todos(
      state.todos,
      action
    ),
    visibilityFilter: visibilityFilter(
      state.visibilityFilter,
      action
    )
  }
};

// with combineReducers
const todoApp = combineReducers({
  todos: todos,
  visibilityFilter: visibilityFilter
});

// with combineReducers where the reducer functions
// are the same name as the property on `state`
const todoApp = combineReducers({
  todos,
  visibilityFilter
});
```

