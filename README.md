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
- Preferences: click `Open User (JSON)` icon top right corner -> Add to settings.json:
```
"editor.formatOnSave": true,
"[javascriptreact]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
```
- add `Auto Rename Tag`

### Configuration file recommended

`jsconfig.json`
```
{
  "compilerOptions": {
    "baseUrl": "./src",
    "checkJs": false,
    "jsx": "react"
  }
}
```


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

### useState

When variables inside a component changes the component is not rerendered -> Use states to change a value in a component
When a state changes the component is rerendered
Each component has its own state
State take some time to be changed, to use the last value of a state in the component body.
Do not use the setValue in the component body it will create infinite loop.

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


### Styling

1. Inline CSS (JSS)

`<div style={{backgroundColor:"red",height:100,}}></div>`

2. CSS file<br>
Risk of confilt

```
# src/global.css

.box {
  height: 100px;
  width: 100px;
}
...
```
```
# src/components/Car/style.module.css

.box {
  height: 200px;
  width: 200px;
}
```
```
# src/App.jsx

import { Car } from "./components/Car/Car";
import "./global.css";

export function App() {
  return (
    <>
      <div>
        <Car /> -> CSS conflict
      </div>
    </>
  );
}
```
```
# src/components/Car/Car.jsx

import "./style.css"
export function Car() {
  return <p className="box">Je suis une voiture</p>;
}
```

3. CSS modules<br>
Safer than CSS file

```
# src/components/Greetings/Greetings.jsx

import s from "./style.module.css";
export function Greetings() {
  return <p className={`${s.box} box`}>Salutation</p>;
}
```

```
# src/components/Greetings/style.module.css

.box {
  height: 100px;
  width: 100px;
}
```
### Maps

```import { MenuListItem } from "../MenuListItem/MenuListItem";
import s from "./style.module.css";
import { DIFFICUTLIES } from "./constant";

export function MenuList({ difficulty, onItemClick }) {
  return (
    <div className={s.container}>
      {DIFFICUTLIES.map((diff) => {
        return (
          <MenuListItem
            onClick={onItemClick}
            difficulty={diff}
            isSelected={difficulty === diff}
          />
        );
      })}
    </div>
  );
}
```

### Filter
```
const users = [
  { name: "Codiku", age: 30 },
  { name: "Julia", age: 22 },
  { name: "Lara", age: 17 },
  { name: "Max", age: 12 },
  { name: "Paul", age: 43 },
  { name: "Ana", age: 68 }
];

const filteredUsers = users.filter(isUserMajor);

function isUserMajor(user) {
  return user.age >= 18;
}
```

### Call APIs

`fetch` function can be used for quering APIs

A cleaner option is to use the library `axios`
`npm i axios`

```
# src/api/tv-show.js

import axios from "axios";
import { BASE_URL, API_KEY_PARAM } from "../config";

export class TVShowAPI {
  static async fetchPopulars() {
    const response = await axios.get(`${BASE_URL}tv/popular${API_KEY_PARAM}`);
    console.log(response.data.results);
    return response.data.results;
  }
}
```
```
# src/App.jsx

import { TVShowAPI } from "./api/tv-show";
...
TVShowAPI.fetchPopulars();
```

### useEffect (hook)

Can be used only:
- At the end of the first rendering of the component
- When specific variable changes, these variables are defined in an array.
- When the component is destroyed or rerendered `return` function is called. Useful for using clearInterval if setInterval was called

```
import { useEffect } from "react"

export function MyComponent() {
  useEffect(() => {
    // Do something
    return () => {     -->  return is optional
      // Do some other thing
    }
  }, [variables, listened])     -->  the array of the variables listened to
```

If the array is empty: useEffect is only executed once 
If there is no array: useEffect is executed at each render

### Boostrap

Include via CDN in index.html https://getbootstrap.com/

Icons:

`npm i react-bootstrap-icons`

## Redux

`npm i @reduxjs/toolkit`
`npm i react-redux`

Recommanded add `Redux DevTools` to Chrome

Add store

```
# src/store/index.js

import { configureStore } from "@reduxjs/toolkit";
import { expenseSlice } from "./expense/expense-slice";

const store = configureStore({
  reducer: {
    EXPENSE: expenseSlice.reducer,  ---> Example of key referencing a slice
  },
});

export { store };
```

```
# src/index.js

...
import { Provider } from "react-redux";
import { store } from "store";
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```
### Slice (group of data in the store)

Setup Slice
```
# src/store/expense/expense-slice.js

import { createSlice } from "@reduxjs/toolkit";

export const expenseSlice = createSlice({
  name: "expenseSlice",
  initialState: {
    expenseList: [{ name: "Ordinateur", price: 300 }],
  },
  reducers: {
    addExpense: (currentSlice, action) => {             ---> Example of action
      currentSlice.expenseList.push(action.payload);
    },
  },
});

const { addExpense } = expenseSlice.actions;

export { addExpense };
```

Update Slice: `useDispatch()`

```
# src/containers/ExpenseInput/ExpenseInput.jsx      ---> components connected to the store are generaly located in src/containers
                                                          usually a specific component used once or twice in all the app
...
import { addExpense } from "store/expense/expense-slice";
import s from "./style.module.css";

export function ExpenseInput(props) {
  const dispatch = useDispatch();              ---> necessary

  function submit(e) {
    e.preventDefault();
    const formData = new FormData(e.currentTarget);
    const name = formData.get("name");
    const price = formData.get("price");
    dispatch(addExpense({ name, price }));               ---> updates the slice
  }
...
```

Read Slice: `useSelector()`

```
import { useSelector } from "react-redux";

export function App() {
  const expenseList = useSelector((store) => store.EXPENSE.expenseList);
```

### Router

```
# src\index.js

...
import { BrowserRouter, Routes, Route } from "react-router-dom";

const root = ReactDOM.createRoot(document.getElementById("root"));

root.render(
  <StrictMode>
    <Provider store={store}>
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<App />} />
          <Route path="/note/:id" element={<NoteRead />} />
          <Route path="/note/new" element={<NoteCreate />} />
          <Route path="*" element={<PageNotFound />} />
        </Routes>
      </BrowserRouter>
    </Provider>
  </StrictMode>
);
```

Nested routes to for componenents sharing elements:

```
index.js

...
root.render(
  <StrictMode>
    <Provider store={store}>
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<App />}>
            <Route path="/" element={<NoteBrowse />} />
            <Route path="/note/:id" element={<NoteRead />} />
            <Route path="/note/new" element={<NoteCreate />} />
            <Route path="*" element={<PageNotFound />} />
          </Route>
        </Routes>
      </BrowserRouter>
    </Provider>
  </StrictMode>
);
```

```
# App.jsx

import { Outlet } from "react-router-dom";

export function App() {
  return <div>Header content
    <Outlet />
  </div>;
}
```
### Navigate

```
# src\components\Header\Header.jsx

import { useNavigate } from 'react-router-dom'

export function Header(props) {
  const navigate = useNavigate()

  return (
  <div className={`row ${s.container}`}>
    <ButtonPrimary onClick={() => navigate("/note/new")}>
...
```

```
# src\components\ButtonPrimary\ButtonPrimary.jsx

export function ButtonPrimary({ children, onClick }) {
  return (
    <button type='button' className='btn btn-primary' onClick={onClick}>
      {children}
    </button>
  )
}
```

### useRef (hook)

Allows easy selection of an HMLT componant without rerendering the componant. Useful for conditionnal focus on inputs.


### useMemo

Works as cache for a the <ins>result of a function</ins>. Is not recomputed if component is rerendered. Is update on when given variables are updated

### memo

Works as cache for a <ins>component</ins>. Is updated only if its `props` are changed.
If a function is passed as `prop`, use `useCallback` (works like `useMemo`) to <ins>store the function</ins> in the parent component so the `prop` will not be considered as changed. 

