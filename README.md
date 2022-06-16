# Offical Melon React Native Guidelines 

This document aims to provide general guidelines for development with React Native in Melon. The goals are to:

- make code easier to maintain and scale
- make it easier to read and quickly understand unfamiliar code
- reduce cognitive load while coding

It is good to have guidlines and rules but they can never be perfect and apply to every single project or code base. Think outside of the box and always strive for readability, simplicity, and clarity!

## Content

- [Naming](#Naming)
  - [File names](#File-names)
  - [React Components](#React Components)
  - [Component Exports](#Component Exports)
  - [Class Prefixes](#Class Prefixes)
  - [React Fragments](#React Fragments)
  - [Variables](#Variables)
  - [Mutable vs Immutable](#Mutable vs Immutable)
  - [Global Constants](#Global Constants)
  - [Styles](#Styles)
  - [Props destructure](#Props destructure)
  - [Props drilling](#Props drilling)
  - [Optional props](#Optional props)
  - [Default state values](#Default state values)
  - [Access modifiers](#Access modifiers)
  - [SafeAreaView](#SafeAreaView)
  - [Ordering](#Ordering)
  - [Keys](#Keys)
  - [Custom hooks](#Custom hooks)
  - [Comments](#Comments)
  - [Absolute imports](#Absolute imports)
  - [Unused Code](#Unused Code)
  - [General Rules](#General Rules)
  - [Custom hooks](#Custom hooks)
- [Creating new Project](#Creating new Project)
- [References](#References)



## Naming 

Descriptive and consistent naming makes software easier to read and understand.

### File names

- Component files and folders should always be named after the exported component inside them.
- `src/*` folders should be `lowercase`
- Component styles should be in a `styles.ts` file unless they have dynamic data and need to be in the component instead.
- Every component dir should have `index.ts` file that exports the component.
- Always use `eslint` to keep the code formated and consistent between different developers and their IDE

### React Components

React components should be functional because there is less boilerplate code and they are faster. They are simple objects instead of classes with internal functionality.

```tsx
//Good
const Component: React.FC = () => {
  return (
    <Child/>
  )
}

//Good
const Component: React.FC = () => <Child/>
```

```tsx
//Avoid
class Component extends React.Component => {
  render() {
    return (
      ...
    )
  }
}
```

### Component Exports

Named exports should be used due to their strict and explicit import syntax. 

```tsx
//Possible
export {Component} from 'components/Component'

//Possible
export {Component as NewName} from 'components/Component'

//Impossible
export {NewName} from 'components/Component'
```

Now in the following example we are going to use default export and show that the component `Component` can be actually imported with any name we want, which introduces a layer of possible human error.

```tsx
//Possible
export Component from 'components/Component'

//Possible
export NewName from 'components/Component'

//Possible
export Anything from 'components/Component'
```

This also forces the reference naming to be explicitly like the component name which means that the PascalCase will be 100% followed


Possible named export reference namings.
```tsx
//Possible
import {NewComponent} from './NewComponent.tsx'

//Impossible
import {newComponent} from './NewComponent.tsx'
import {newcomponent} from './NewComponent.tsx'
import {Newcomponent} from './NewComponent.tsx'
```

Possible default export reference namings.

```tsx
//Possible
import NewComponent from './NewComponent.tsx'
import newComponent from './NewComponent.tsx'
import newcomponent from './NewComponent.tsx'
import Newcomponent from './NewComponent.tsx'
```

### React Fragments

React fragments `<></>` should be used when possible, instead of `<View/>` because it doesn't create a node in the DOM tree. This means that the DOM tree won't be cluttered with unnecessary elements.

```tsx
//Good
const Component: React.FC = () => {
  return (
    <>
      <Child/>
      <Child/>
    </>
  )
}

//Good
const Component: React.FC = () => {
  return (
    <View style={styles.children}>
      <Child/>
      <Child/>
    <View/>
  )
}
```

```tsx
//Avoid
const Component: React.FC = () => {
  return (
    <View>
      <Child/>
      <Child/>
    <View/>
  )
}

//Avoid
const Component: React.FC = () => {
  return (
    <>
      <View style={styles.children}>
        <Child/>
        <Child/>
      <View>
    </>
  )
}
```

### Variables

Avoid using `vars`, their scope might be problematic when it comes to React and it's VDOM re-rendering. Use `const` and `let` instead.

```tsx
//Good
const filteredItems = items.filter(...)
let filteredItems = items.filter(...)
```

```tsx
//Avoid
var filteredItems = items.filter(...)
```

### Mutable vs Immutable 

Avoid mutating variables, it's exteremly dangerous and can lead to problematic situations like not re-rendering. Use immutable data or `useState` instead.

```tsx
//Good
const [state, setState] = useState(DEFAULT_STATE)

setState(newState)
```

```tsx
//Avoid
const [state, setState] = useState(DEFAULT_STATE)

state = newState

//Avoid
let state = DEFAULT_STATE

state = newState
```

### Global Constants

Unlike the other variables, global constants are all caps with underscore syntax to distinguish the difference easily. Ex: `ALL_CAPS`.

```tsx
//Good
const HOME_SCREEN = "Home screen"
```

```tsx
//Avoid
const HomeScreen = "Home screen"
const Home_Screen = "Home screen"
const homeScreen = "Home screen"
const HOMESCREEN = "Home screen"
```

Make sure to freeze variables with data type of objects and arrays, because they might be mutated which can also lead to unwanted situations.

```tsx
//Good
const DEFAULT_OPTIONS = Object.freeze([1,2,3,4])

```

```tsx
//Avoid
const DEFAULT_OPTIONS = [1,2,3,4]
```


### Styles

Avoid inline styles or `const` variables without `StyleSheet.create`. 

```tsx
//Good

const styles = StyleSheet.create({
  background: {
    backgroundColor: 'black'
  }
})

<Component style={styles.background} />
```

```tsx
//Avoid

<Component style={{backgroundColor: 'black'}} />

//Avoid

const styles = {
  background: {
    backgroundColor: 'black'
  }
}

<Component style={styles.background} />
```

### Props destructure

Destructuring the props object increases the readability and makes it easier to detect unused props.

```tsx
//Good
export const Component: React.FC<ComponentProps> = ({id, name, surname}) => {
  return (
    <>
      <Text>{id}</Text>
      <Text>{name}</Text>
      <Text>{surname}</Text>
    </>
  )
}
```

```tsx
//Avoid
export const Component: React.FC = (props: ComponentProps) => {
  return (
    <>
      <Text>{props.id}</Text>
      <Text>{props.name}</Text>
      <Text>{props.surname}</Text>
    </>
  )
}

//Avoid
export const Component: React.FC = (props: ComponentProps) => {
  const {id, name, surname} = props
  return (
    <>
      <Text>{id}</Text>
      <Text>{name}</Text>
      <Text>{surname}</Text>
    </>
  )
}
```

### Props drilling

Props drilling should be used only when necessary with the proper names to keep the declerative syntax as readable as possible.

In this example we pass the same function to two different props because it makes it obvious where this function will be called and makes it scalable for the future, if we want to pass another function for update we just need to replace that prop.

```tsx
//Good
<Component 
  onClickUpdate={editUser} 
  onClickAdd={editUser}
/>
```

In this second example we pass the function to only 1 prop, however we have no idea where it's used and in how many places, this decreases the readability, and ruins the scalability of the component.

```tsx
//Avoid
<Component 
  editUser={editUser}
/>
```

### Optional props

Declare interface props as optional with `?` where a `undefined` value is acceptable and the prop is not requied to be passed down to the component.

```tsx
interface Names {
  surname?: string;
}

//Possible
<Names surname="Surname" />

//Possible
<Names surname={undefined} />

//Possible
<Names />
```

If the prop might be `undefined` but you want it to be always required, you should use the following syntax instead

```tsx
interface Names {
  surname: string | undefined;
}

//Possible
<Names surname="Surname" />

//Possible
<Names surname={undefined} />

//Impossible
<Names />
```

### Default state values

Avoid using `undefined` or `null` for default values and use the appropriate default value of that given type.

```tsx
//Good
const [counter, useCounter] = useState<number>(0)

//Good
const [counter, useCounter] = useState<number>()
```

```tsx
//Avoid
const [counter, useCounter] = useState<number | null>(null)

//Avoid
const [counter, useCounter] = useState<number | undefined>(undefined)
```

### Access modifiers

Access modifiers are not needed in React because of the functional components and their declerative syntax, it's not possible to expose any of the variables even if they are public.

### SafeAreaView

All of the screens should be wrapped in `SafeAreaView` to render the content within the safe area boundaries of the devices.

### Ordering

- 1 Imports
- 2 Interfaces / Types
- 3 Global variables
- 4 Component
- 4.1 Hooks
- 4.2 Variables
- 4.3 Functions
- 4.4 `return`

```tsx
//Good
import {Button, Text} from 'react-native'
import styles from './styles.ts'

interface ComponentProps {
  id?: string;
}

interface Names {
  name: string;
  surname: string;
}

export const Component: React.FC<ComponentProps> = ({id}) => {
  const navigation = useNavigation()
  const [names, setNames] = useState<Names>({
    name: "",
    surname: ""
  })

  useEffect(() => {
    ...
  }, [])

  const doSomething = () => {
    ...
  }

  return (
    <>
      <Text>Something</Text>
      <Button onClick={doSomething}>Click me!</Button>
    </>
  )
}
```

### Keys
Avoid using `index` when it comes to keys, they are not unique enough and can lead to complex situations. For example, if we are to iterate 2 arrays and use their indexes as keys, we would have duplicated keys. You may use them if the array items don't have stable ids, however it's not safe and other id should be used instead.

```tsx
//Good
return (
  <>
    {users.map((user, index) => {
      return (
        <View key={user.id}>
          ...
        </View>
      )
    })}
  </>
)
```

```tsx
//Avoid
return (
  <>
    {users.map((user, index) => {
      return (
        <View key={index}>
          ...
        </View>
      )
    })}
  </>
)
```


### Custom hooks
Always name your hooks with `use` prefix and `camelCase` syntax

```tsx
//Good
export const useBoolean = () => {
  ...
}
```

```tsx
//Avoid
export const UseBoolean = () => {
  ...
}
```

```tsx
//Avoid
export const dynamicBoolean = () => {
  ...
}
```

### Comments

- When they are needed, use comments to explain **why** a particular piece of code does something. Comments must be kept up-to-date or deleted.

- Avoid block comments inline with code, as the code should be as self-documenting as possible.

- Use `/** + Enter` to generate automatic documentation for functions, hooks and components. This will allow the IDE to automatically show the necessary information on hover.

```tsx
//Good

/**
 * Hook that allows the user to handle boolean value easily.
 * @param defaultValue - Optional parameter that can be used to set the initial value to `true`.
 * @returns boolean
 * @example const {bool, setTrue, setFalse} = useBoolean(true)
 */
export const useBoolean = (defaultValue: boolean) => {
  ...
}
```

```tsx
//Avoid

//Hook that allows the user to handle boolean value easily.
export const useBoolean = (defaultValue: boolean) => {
  ...
}
```

### Absolute imports

Always use absolute import paths instead of relative ones, they are much better when it comes to project scalability and they work in case of directory change.


If we change the location of the component importing the code below, the imports would still work becase they are using the absolute path which is always the same.
```tsx
//Good
import {ChildComponent} from "components/ChildComponent"
import {useBoolean} from "hooks/useBoolean"
```

However if we were to move this component in another directory, for example one level above, the code below won't work because of the relative import path.

```tsx
//Avoid
import {ChildComponent} from "../../components/ChildComponent"
import {useBoolean} from "../../hooks/useBoolean"
```

### Unused Code

Unused (dead) code, including React Native template code and placeholder comments should be removed. Imported files that contain unused code on global level will increase the bundle size.


### General Rules

- Avoid Class components.
- use `PascalCase` for components and types, `camelCase` for everything else
- prefix custom hooks with `use`. Ex: `useModal`
- prefix callback props with `on`. Ex: `onConfirm`
- Name booleans like `isSpaceship`, `hasSpacesuit`, etc. This makes it clear that they are booleans and not other types.

# Creating new Project

**Warning!** Make sure you meet all of the requirements listed in the official React Native [environment setup](https://reactnative.dev/docs/environment-setup) before you start creating a new project.

**Warning!** Do not use any special characters or spaces in the name of the project when replacing `PROJECT_NAME`. Use `-` to separate multiple words. Ex: `melon-react-app`

Creating a new **React CLI** project is quite simple. Just run the following command:

1. `npx react-native init PROJECT-NAME --template react-native-template-typescript`

If you want to generate the project with **expo**, run the following commands instead:

1. `yarn global add expo-cli` or `npm install --global expo-cli`

2. `expo init PROJECT-NAME -t expo-template-blank-typescript`

# References
* [The Official React documentation](https://reactjs.org/)
* [The Official React Native documentation](https://reactnative.dev/)
* [Airbnb React Style Guide](https://github.com/airbnb/javascript/tree/master/react/)
* [The Official TypeScript documentation](https://www.typescriptlang.org/docs/)
