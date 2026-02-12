

1. Install Node.js (if not installed). 
```
node -v
npm -v
```
2. Setup react vite
```react
npm create vite@latest my-app
```

3. npm install in the dir
4. npm run dev


```
my-app/
 ├─ src/
 │   ├─ App.jsx
 │   ├─ main.jsx
 │   └─ assets/
 ├─ index.html
 ├─ package.json
 └─ vite.config.js
```

**Key files:**
- `main.jsx` → React entry point
- `App.jsx` → Main component
- `vite.config.js` → Vite configuration

```
npm install react-router-dom //for routing
npm install axios
```

## Basics
---
##### 1. A component = reusable UI function.
----
```node
function Welcome() {
  return <h1>Hello Leo</h1>
}

export default Welcome
```

use it like this
```node
<Welcome />
```

##### 2.JSX (HTML inside JavaScript)
---
```node
function App() {
  const name = "Leo"

  return (
    <div>
      <h1>Hello {name}</h1>
    </div>
  )
}
```
##### 3.Props (Passing Data Between Components)
---
Props = parameters for components.
```node
<Profile name="Leo" age={20} />

// in another maybe, profile.jsx
function Profile({ name, age }) {
  return <h1>{name}</h1>
}
```

##### 4.State (Dynamic Data)
---
State lets UI update automatically.

Use `useState`
```node
import { useState } from "react"

function Counter() {
  const [count, setCount] = useState(0)
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>
        Add
      </button>
    </div>
  )
}
```
**Key Idea: State changes → React re-renders component**

##### 5.Rendering Lists
---
```node
const users = ["Leo", "John", "Anna"]
return (
  <ul>
    {users.map((user, index) => (
      <li key={index}>{user}</li>
    ))}
  </ul>
)
```

##### 6. useEffect
---
Runs when component loads or updates.
```node
import { useEffect } from "react"
useEffect(() => {
  console.log("Component mounted")
}, [])
```

##### 7.Fetching Data
---
```node
import { useState, useEffect } from "react"

function Users() {
  const [users, setUsers] = useState([])

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => setUsers(data))
  }, [])

  return (
    <div>
      {users.map(u => (
        <p key={u.id}>{u.name}</p>
      ))}
    </div>
  )
}
```

##### 8.Routing
---
```node
import { BrowserRouter, Routes, Route } from "react-router-dom"

<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
  </Routes>
</BrowserRouter>
```

##### 9.CSS
---
Normal CSS is **global** → can cause conflicts.
CSS Modules make styles **scoped** to a component.
```node
App.module.css //file name

import styles from "./App.module.css"

export default function App() {
  return <div className={styles.box}>Hello</div>
}
```