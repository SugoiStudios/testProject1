# testProject1

Task, build an HOC that allows you to write functional components with ALL the functionalities of class Components without using hooks.

> MakeStateful Its an "HOC" allows you to write functional components with ALL the functionalities of class Components without using hooks. *
## Basic API

In order to fully succeed in this exercise the functionality of the component should be the one in the following example

```jsx
import makeStateful from 'react-makestateful'

const increaseByOneOnClick = ({makeState, getProps, on, statefulProvider}) => {
  // Behaves similar to use state
  const [getNumber, setNumber] = makeState('number')(0)

  // getProps returns the props at the give stage of the execution, here you will print the initial props
  console.log('initial props =>' getProps() )

  // on.<lifeCycleMethod> is a setter that adds the function to the lifecycle
  on.didUpdate = (prevProps, scopedPrevState, snapshot) => {
    console.log('did Something at every update')
  }

  // --
  const onClick = () => {
    console.log('current number:', getNumber())
    setNumber(getNumber() + 1)
  }

  // add some extra functionality like you would do with hooks
  // stateful provider provides a scope to the state and lifecycle access of logic providers
  
  // this is the function you will actually render no props will be provided, use getProps instead
  return () => {
    return(
      <div>
        <div style={getProps().style2.propShowStyle}> {JSON.stringify(getProps())}<div>
        <button onClick={onClick}> increase by 1 </button>
        <div> current Count : {getNumber()}</div>
      </div>
    )
  }
}

```

The basic component that should be passed to ```makeStateful``` is a function that returns the component you actually want to render. Everything else before that will be executed only once (like the class constructor) when the component is instantiated. 
This is the expected behavior of the component

## makeState API

This HOC should be able to create a state member by using a function called makeState.
1. makeState should return a getter and setter function for your state entry.
2. makeState function requires a value and an option name.
3. If no name is passed the name will be set as ```"default"```.

### Syntax

```js
  [stateGetter, stateSetter] = makeState(name)(value)
```

- **name (String)**: a unique name within your component. If not defined it will default to 'default'
- **value**: the initial state value
- **stateGetter (function)**: a function that returns the current value of your state
- **stateSetter (function)**: a function to set the state

### State - Setter

The state setter calls can be used in the same way as react's setState

```js
  stateSetter(updater, [callback]) 
``` 

- **updater (any)**: if the update is a function it will be called as ```updater(state)```

The only difference with react setState is that the updater will be already wrapped in a function call when calling react's setState

## Props

The props are accessible through the ```getProps()``` method. When called within the component setup it will return the initial props. Calls within the render component will return the props at the specific render time.

### Adding lifecycle methods
Makes stateful allows you to access ALL the lifecycle methods of React Class Components.
To add any of the available lifecycle methods in react you need to call ```on.<lifecyclemethod>```. **ATTENTION**, I removed the word 'component' from lifecycle methods since it's superfluous.

### Syntax
```js
  on.shouldUpdate = Your_shouldUpdate_function
  on.getSnapshotBeforeUpdate = Your_getSnapshotBeforeUpdate_function
  on.didMount = Your_didMount_function
  on.didUpdate = Your_didUpdate_function
  on.willUnmount = Your_willUnmount_function
  on.didCatch = Your_didCatch_function
```

### Implementation Quirks
To improve readability and reduce parentheses, the behavior of ```on.<lifecyclemethod>``` does not set your method, but rather adds it to a list of methods to be executed during the specific lifecycle method. For example:

```js
  on.didMount = () => console.log('this will execute first on did mount')
  on.didMount = () => console.log('this will execute second on did mount')
  on.didMount = () => console.log('this will execute third on did mount')
```

The call other is guaranteed to be the same as the other in which the function were added.

## Isolating logic API (Optional - Extra)

Just like hooks it should be super easy to add external logic to this HOC. Moreover, in order to isolate the access to state, it should be possible to scope lifecycle methods and makeState. This should not only remove possible conflicts when setting state, but also limit the access to the full state from logic providers during the calls to ```getSnapshotBeforeUpdate```, ```componentDidUpdate```, and ```shouldComponentUpdate```. Within your logic provider you should be able to use ```makeState```, and the ```on``` setters just like you would have done if you had a regular component. 

### Syntax
```js
  {makeState, getProps, addContext, on} = statefulProvider(name)
```

