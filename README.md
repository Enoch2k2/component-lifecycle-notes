# Component Lifecycles
## **Agenda**
- Learn about Mounting phase
  - constructor
  - getDerivedStateFromProps
  - UNSAFE_componentWillMount
  - render
  - componentDidMount
- Learn about Update Phase
  - UNSAFE_componentWillReceiveProps
  - shouldComponentUpdate
  - UNSAFE_componentWillUpdate
  - getSnapshotBeforeUpdate
  - componentDidUpdate
- Learn about Unmount Phase
  - componentWillUnmount

## Mounting Phase
### Constructor
```
constructor(props) {
  super(props)
  this.state = {}
}
```
- call on super in order to use this in the constructor
- set default state
- bind this (the component) to functions

### getDerivedStateFromProps
```
getDerivedStateFromProps(props, state) { 
  const { lastPetType } = props
  const { currentPetType } = state
  
  if (currentPetType === lastPetType) { 
    return null
  }
  
  return {
    currentPetType: lastPetType,
  }
}
```
- is replacing `componentWillReceiveProps`
- This component gets called during each update lifecycle caused by a change in the props and will be passed all the props(changed and unchanged) and the current state. This component is important if any part of your component has some of its state that is dependent on the passed in props. Calling this.setState here won't cause any extra rendering calls.
- reason we would use this is performance as to no extra rendering of the component will occur if there's no change
Inside of getDerivedStateFromProps
  - you should: sync the state to props
  - you shouldn't: Create side effects, so no fetchs or async actions.

### UNSAFE_componentWillMount
- is replacing `componentWillMount`
- is going to be removed from React as there is no need for it and shouldn't be used.

### render
```
render () {
  const { avatar, username } = this.props

  return (
    <React.Fragment>
      <UserAvatar src={avatar} />
      <UserProfile username={username} />
    </React.Fragment>
  )
}
```
- This method is one of the few required methods, it examines this.props and this.state to return either a React element, string/numbers, null, or booleans.
- Inside of render,
  - you should: keep it pure, and return one of the above methoded types.
  - you shouldn't: Create side effects, modify state, interact with the browser directly.

### componentDidMount
```
componentDidMount() {
  this.getPetData();
}
```
- This is the last action that occurs at end of the mounting phase, it occurs only when that component and all its sub-components have rendered properly.
- This makes it perfect for performing any actions that would cause side-effects. Since this method doesn't run until your component is mounted, you can do all the setup you couldn't do without a DOM here.
- Inside of componentDidMount
  - you should: cause side effects, add event listeners, interact directly with the DOM.
  - you shouldn't: call this.setState, this will cause a re-render.

## Updating Phase
### UNSAFE_componentWillReceiveProps
```
UNSAFE_comonentWillReceiveProps(nextProps) {
  if(this.props.percent !== nextProps.percent) {
    this.setUpCircle(nextProps.percent)
  }
}
```
- As the name implies, this lifecycle method is going to be removed, it was previously called `componentWillReceiveProps`.
- `getDerivedStatefromProps` should be used instead if you do need to compare the new state and props.

### shouldComponentUpdate
```
shouldComponentUpdate(nextProps, nextState) {
  return this.props.engagement !== nextProps.engagement
    || nextState.input !== this.state.input
}
```
- This method runs to let a component know if the output has changed due to changes in the state or props. By default this method defaults to always re-render on every state change, this is generally always what you would want to happen.
- If the state changes but you see no need to re-render the component, you could customize when it should render using this function

### UNSAFE_componentWillUpdate
- Again this lifecycle method is going to be removed, it was previously called `componentWillUpdate`.
- This component used to run between `shouldComponentUpdate` and `render`, there was rarely any use for it so it is now going to be removed.

### getSnapshotBeforeUpdate
```
getSnapshotBeforeUpdate(prevProps, prevState) {
  if (prevProps.list.length < this.props.list.length) {
    const list = this.listRef.current;
    return list.scrollHeight - list.scrollTop;
  }
  return null;
}
```
- This method is invoked before any rendered output is commited to the DOM.
- Taking a snapshot allows you to capture all the current values on the DOM, eg. Scroll Position before they change. The return value of method will get passed to `componentDidUpdate`.

### componentDidUpdate
```
componentDidUpdate(prevProps, prevState, snapshot) {
  if (snapshot !== null) {
    const list = this.listRef.current;
    list.scrollTop = list.scrollHeight - snapshot;
  }
}
```
- This method allows us to update our DOM in response to prop or state changes. This is also a good place to do async request as long as you compare the current and previous props. 
- We can also use our snapshot data from `getSnapshotBeforeUpdate` here.
- Inside of componentDidUpdate
  - you should: Update the DOM based on prop or state changes, cause side effects.
  - you shouldn't: call this.setState.

## UnMounting Phase
### componentWillUnmount
```
componentWillUnmount() {
  window.removeEventListener('addPet', this.addPet) 
}
```
- This function currently is the only method that runs during the unmounting phase of a component.
  - happens at the very end of the component lifecycle
- It can be considered the clean up phase of a component's lifecycle.
  - This is the best method to clean up anything created during the lifespan of the unmounting component.
- Inside of a componentWillUnmount
  - you should: cancel any outgoing request, remove event listeners, remove timers.
  - you shouldn't: do anything but clean up.

### Phases in order
## Mounting Phase
```
Constructor -> getDerivedStateFromProps -> render -> componentDidMount
```

## Update Phase
```
some event trigger -> getDerivedStateFromProps -> shouldComponentUpdate -> render -> getSnapshotBeforeUpdate -> componentDidUpdate
```

## Unmount Phase
```
componentWillUnmount
```
