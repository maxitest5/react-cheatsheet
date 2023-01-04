# react-cheatsheet

## Init

Install : Node, npm, npx

run `npx create-react-app my-app`


## React basics

### Basic component
```
# index.js

// Import some react tools
import ReactDOM from "react-dom/client";
// Import our first component : App
import { App } from "./App";
// Target the root div
const rootDiv = document.getElementById("root");

// Transform the root div into a react node
const reactRoot = ReactDOM.createRoot(rootDiv);

// Inject our App component into the react node
reactRoot.render(<App />);
```

```
# App.jsx

export function App() {
  return "Hello";
}
```

### VS Code - Recommanded extensions

- add `Prettier - Code formatter`
- Format document With.. (Crtl + Maj + P) -> Configure default formater 
- Preferences: Open User Settings (JSON) -> Add `"editor.formatOnSave": true,` to settings.json
- add `Auto Rename Tag`

### Props

Props cannot be changed inside the component where they are used
Props can be changed where the component is initilized -> rerender of the component


```
# Grettings.jsx

export function Greetings(props) {
  console.log(props);
  return (
    <div>
      Hi {props.firstName} {props.lastName} you are {props.age + 10}
    </div>
  );
}
```

```
# In another component

import { Greetings } from "./Greetings";
...
<Greetings
  firstName={"Rachel"}
  lastName={"Green"}
  age={30}
>
```
<br />

### States

When variables inside a component changes the component is not rerendered -> Use states to change value in a component
Each component has its own state

Exemple : `const[age,setAge] = useState(30)`

```
# AgeCounter.jsx

import { useState } from "react";
import { AgeDisplay } from "./AgeDisplay";

export function AgeCounter(props) {
  const [age, setAge] = useState(30);

  function increaseAge() {
    setAge(age + 1);
  }
  console.log("rerender !");
  return (
    <div>
      <button onClick={increaseAge}>Increase age</button>
      <AgeDisplay age={age} />
    </div>
  );
}
```

```
# AgeDisplay.jsx

export function AgeDisplay(props) {
  return <p>You are {props.age} years old</p>;
}
```

