# React Frontend Framework 

## Fundamentals
- **Core Philosophy**: Understand that React is a declarative, component-based library for building user interfaces.
- **JSX (JavaScript XML)**: The syntax extension for JavaScript. You need to be comfortable with embedding JavaScript expressions ({}), using attributes (className instead of class), and the fact that it must return a single root element.
- **Virtual DOM**: Understand the concept of the Virtual DOM and how React uses it to efficiently update the actual DOM.
- **Components**: The building blocks of React apps.
    - **Functional Components**: The modern standard.
    - **Class Components**: The older syntax. You must be able to read and understand them, as they are common in legacy codebases.
- **Props vs. State**: This is a critical distinction.
    - **Props (Properties)**: Immutable data passed down from a parent component to a child.
    - **State**: Mutable data that is managed within a component and can change over time, causing the component to re-render.
- **Conditional Rendering**: Displaying UI based on certain conditions, using if-else, ternary operators (? :), or logical && operators.
- **Lists and Keys**: Rendering a list of items using the .map() function. The key prop is essential here for performance and identity.
- **Event Handling**: Managing user interactions like onClick, onChange, etc.

## Hooks
Hooks are functions that let you "hook into" React state and lifecycle features from function components. They are the core of modern React development.
- **Rules of Hooks**: Only call Hooks at the top level. Only call Hooks from React function components or custom Hooks.

### Basic Hooks
#### useState
- Add state variable to a functional component. "State" is any data that a component needs to remember and change, causing the component to re-render.
```jsx
import React, { useState } from 'react';

function Counter() {
  // 1. Declare state: `count` is the value, `setCount` is the updater function.
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      {/* 2. Update state on click */}
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

#### useEffect
- Handles side effects in a component. A side effect is any work that doesn't directly relate to rendering the UI, such as fetching data from an API, setting up a subscription, etc.
- You pass useEffect a function (the "effect") and an optional dependency array.
    - React runs the effect after every render if you don't provide a dependency array.
    - If you provide an empty array ([]), the effect runs only once, after the initial render.
    - If you provide an array with variables `([prop, state])`, the effect will re-run only if any of those variables change between renders.
```jsx
import React, { useState, useEffect } from 'react';

function TitleUpdater() {
  const [count, setCount] = useState(0);

  // 1. The effect function that will run after render
  useEffect(() => {
    // This is the side effect: interacting with the browser DOM
    document.title = `You clicked ${count} times`;
    console.log('Effect ran!');
  }, [count]); // 2. The dependency array: re-run only if `count` changes

  return (
    <div>
      <p>Check the browser tab title!</p>
      <button onClick={() => setCount(count + 1)}>
        Click me ({count})
      </button>
    </div>
  );
}
```

#### useContext
- Lets a component access data from a "Context" without having to pass that data down through multiple levels of components via props. It provides a way to share state globally across a tree of components.
- Using useContext is a three-step process:
    - Create a Context: Use React.createContext() to create a context object.
    - Provide the Context: Wrap your component tree with the context's Provider component and pass it a value prop. Any component inside this provider can now access that value.
    - Consume the Context: In any child component, call useContext() with the context object you created to read the value.
```jsx
import React, { useState, useContext, createContext } from 'react';

// 1. Create a context
const ThemeContext = createContext('light');

function App() {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(currentTheme => (currentTheme === 'light' ? 'dark' : 'light'));
  };

  // 2. Provide the context value to the component tree
  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={toggleTheme}>Toggle Theme</button>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  // Toolbar doesn't need to know about the theme.
  // It just passes the component down.
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  // 3. Consume the context value
  const theme = useContext(ThemeContext);

  const style = {
    background: theme === 'dark' ? '#333' : '#FFF',
    color: theme === 'dark' ? '#FFF' : '#333',
    padding: '10px',
    border: '1px solid black'
  };

  return <button style={style}>I am a {theme} button</button>;
}
```


### Additional Hooks
#### useReducer
- It is an alternative to useEffect for managing more complex state logic. Instead of just updating a value directly, you dispatch "actions" that are handled by a "reducer" function to produce the new state.
- It's best used when you have state that involves multiple sub-values or when the next state depends on the previous one in a complex way. It helps by centralizing all your state update logic into a single reducer function, making your component's code cleaner and state transitions more predictable and manageable.
```jsx
import React, { useReducer } from 'react';

// 1. Define the initial state
const initialState = { count: 0 };

// 2. Create the reducer function to handle actions
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return initialState;
    default:
      throw new Error();
  }
}

function ReducerCounter() {
  // 3. Initialize the hook with the reducer and initial state
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      {/* 4. Dispatch actions to update state */}
      <button onClick={() => dispatch({ type: 'increment' })}>Increment</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>Decrement</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

#### useCallback
- Returns a memoized (cached) version of a callback function. This means useCallback will return the eact same function instance between re-renders, instead of creating a new one each time, as long as its dependencies haven't changed.
- It's main purpose is performance optimization. In JavaScript, functions are objects. So, on every render a new function object is created.f you pass this function as a prop to a child component that is optimized (e.g., with React.memo), the child will re-render unnecessarily because it receives a "new" prop (a new function reference) every time. useCallback prevents this by ensuring the child receives the same function prop, thus avoiding a re-render.
```jsx
import React, { useState, useCallback } from 'react';

// A child component that is optimized to not re-render if its props are the same
const Button = React.memo(({ onIncrement }) => {
  console.log('Button rendered!');
  return <button onClick={onIncrement}>Increment Count</button>;
});

function App() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // Without useCallback, this function is re-created on EVERY App re-render.
  // const handleIncrement = () => setCount(count + 1);

  // With useCallback, this function is only re-created when `count` changes.
  const handleIncrement = useCallback(() => {
    setCount(prevCount => prevCount + 1);
  }, []); // Empty dependency array means the function is created only once.

  return (
    <div>
      <p>Count: {count}</p>
      {/* When this button is clicked, only App re-renders, not Button */}
      <button onClick={() => setOtherState(!otherState)}>
        Toggle Other State
      </button>
      <Button onIncrement={handleIncrement} />
    </div>
  );
}
```

#### useMemo
- It is very similar to useCallback, but instead of memoizing a function, it memoizes the return value of a function. It caches the result of a calculation.
- If you have a function that takes a long time to compute a value (like filtering a massive array or complex math), useMemo will cache its result and only re-calculate it when one of its dependencies changes.
```jsx
import React, { useState, useMemo } from 'react';

const slowFunction = (num) => {
  console.log('Calling Slow Function...');
  for (let i = 0; i < 1000000000; i++) {} // Simulate heavy work
  return num * 2;
};

function App() {
  const [number, setNumber] = useState(1);
  const [dark, setDark] = useState(false);

  // useMemo will only re-run slowFunction when `number` changes.
  // Changing `dark` will not trigger the re-calculation.
  const doubleNumber = useMemo(() => {
    return slowFunction(number);
  }, [number]);

  const themeStyles = {
    backgroundColor: dark ? 'black' : 'white',
    color: dark ? 'white' : 'black'
  };

  return (
    <div>
      <input
        type="number"
        value={number}
        onChange={e => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark(prevDark => !prevDark)}>
        Change Theme
      </button>
      <div style={themeStyles}>Calculated Number: {doubleNumber}</div>
    </div>
  );
}
```
#### useRef
- It provides a reference object, This objext has a special property called `.current` that you can assing a value to. This object persists for the full lifetime of the component and updating it doesn't trigger a re-render.
- It has two primary use cases: accessing DOM nodes, storing a mutable value (say timerID or a previous state value).
```jsx
import React, { useRef } from 'react';

function FocusInput() {
  // 1. Create a ref object
  const inputRef = useRef(null);

  const handleClick = () => {
    // 3. Access the DOM node via the .current property
    inputRef.current.focus();
  };

  return (
    <div>
      {/* 2. Attach the ref to a DOM element */}
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>Focus the input</button>
    </div>
  );
}
```

#### useLayoutEffect
- It has the exact same signature as useEffect, but it fires synchronously after all DOM mutations are calculated, but before the browser paints the changes to the screen.
- You use it in the rare case that your effect needs to measure the DOM (e.g., get an element's height or position) and then trigger a synchronous re-render to update the UI based on that measurement. Using useEffect for this can cause a visual "flicker," where the user briefly sees the initial state before it's corrected.
```jsx
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip() {
  const buttonRef = useRef(null);
  const [tooltipTop, setTooltipTop] = useState(0);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      // Measure the button's position
      const { bottom } = buttonRef.current.getBoundingClientRect();
      // Update state *before* the browser paints
      setTooltipTop(bottom + 10);
    }
  }, []);

  return (
    <div>
      <button ref={buttonRef}>Hover over me</button>
      <div style={{ position: 'absolute', top: `${tooltipTop}px` }}>
        This is a tooltip!
      </div>
    </div>
  );
}
```

#### useDebugValue
- It is a special hook that lets you display a custom label for your custom hooks in the React DevTools inspector.
```jsx
import { useState, useEffect, useDebugValue } from 'react';

// A custom hook to track a friend's online status
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    // ... logic to subscribe to friend's status ...
    // For this example, we'll just simulate it.
    setIsOnline(true);
  }, [friendID]);

  // Use the debug hook to display the status in DevTools
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

function ChatComponent({ friendID }) {
  const isOnline = useFriendStatus(friendID);
  return <div>Friend is {isOnline ? 'Online' : 'Offline'}</div>;
}
```

#### useDeferredValue
- Lets you defer updating a non-urgent part of the UI. It accepts a value and returns a new "deferred" copy of that value. This deferred value will "lag behind" the original if React is busy with more urgent updates (like user input).
- To improve UI responsiveness. Imagine typing into a search field that filters a very long list. Without this hook, each keystroke could cause a slow, janky re-render of the list, making the input field feel stuck. useDeferredValue allows the input to update instantly while telling React that updating the list is a lower priority and can be done when the browser isn't busy.
```jsx
import React, { useState, useDeferredValue } from 'react';

// A component that is intentionally slow to render
const SlowList = ({ text }) => {
  // ... logic to generate a very long list based on `text` ...
  return <ul>{/* ... list items ... */}</ul>;
};

function SearchPage() {
  const [query, setQuery] = useState('');

  // 1. Create a deferred version of the query state
  const deferredQuery = useDeferredValue(query);

  function handleChange(e) {
    setQuery(e.target.value);
  }

  return (
    <div>
      {/* 2. The input is fast, controlled by the original `query` */}
      <input value={query} onChange={handleChange} placeholder="Search..." />

      {/* 3. The slow list uses the deferred value, so it won't block the input */}
      <SlowList text={deferredQuery} />
    </div>
  );
}
```

#### useTransition

#### useOptimistic
- Lets you instantly show a "fake" or "optimistic" state to the user while the real asynchronous action (like a network request) is still in progress.
- To make applications feel incredibly fast. When a user sends a chat message or "likes" a post, they want to see the result immediately. useOptimistic allows you to update the UI as if the action has already succeeded. If the action eventually fails, React will automatically and seamlessly revert the UI back to its original state.
```jsx
import React, { useOptimistic } from 'react';
// Assume `sendMessageAPI` is a function that sends a message to a server
import { sendMessageAPI } from './api';

function MessageThread({ messages, sendMessage }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    // This function merges the current state with the optimistic update
    (currentState, newMessageText) => [
      ...currentState,
      { text: newMessageText, sending: true, id: Math.random() },
    ]
  );

  async function formAction(formData) {
    const messageText = formData.get('message');
    addOptimisticMessage(messageText);
    // Now, attempt the real action
    await sendMessage(messageText);
  }

  return (
    <div>
      {optimisticMessages.map((msg) => (
        <div key={msg.id}>
          {msg.text}
          {msg.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction}>
        <input type="text" name="message" placeholder="Type..." />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

#### Custom Hooks
- A custom hook is a reusable JavaScript function whose name starts with the prefix "use" and that can call other React hooks. It's a convention that tells React and developers that this function follows the rules of hooks.
- The primary goal is to extract and reuse stateful logic from components.
```jsx
// file: useFetch.js
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Reset state for new URL
    setLoading(true);
    setData(null);
    setError(null);

    fetch(url)
      .then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then(data => {
        setData(data);
      })
      .catch(error => {
        setError(error);
      })
      .finally(() => {
        setLoading(false);
      });
  }, [url]); // Re-run effect if the URL changes

  // Return the state for the component to use
  return { data, loading, error };
}

export default useFetch;
```

## Advanced Concepts & Patterns
- **Component Composition**
- **Context API**
- **Higher-Order Components**
- **Error Boundaries**
- **Portals**
- **Fragments**

### Components
- <Profiler>: lets you measure rendering performance of a React tree programmatically.
- <StrictMode>: enables extra development-only checks that help you find bugs early.
- <Suspense>: lets you display a fallback while the child components are loading.
```jsx
<Suspense fallback={<Loading />}>
  <SomeComponent />
</Suspense>
```
- <Fragment>: alternatively written as <>...</>, lets you group multiple JSX nodes together.

## React Ecosystem
- State Management:
    - Lifting State Up: The fundamental React pattern for sharing state.
    - Redux: The classic library for global state management. You should understand its core concepts (Store, Actions, Reducers).
    - Redux Toolkit (RTK): The modern, official, and recommended way to use Redux. It dramatically simplifies Redux code.
    - Zustand / Jotai: Lighter, more modern alternatives to Redux that are gaining popularity.
    - TanStack Query (React Query): The de-facto standard for managing server state. It handles fetching, caching, and updating data from APIs, making your life much easier.
- Routing:
    - React Router: The standard library for handling navigation in a single-page application (SPA). Key concepts include <BrowserRouter>, <Routes>, <Route>, <Link>, and the useNavigate and useParams hooks.

## Performance & Testing
Writing an app is one thing; making sure it's fast and reliable is another.
- Performance Optimization:
    - React.memo: A higher-order component that memoizes a component, preventing it from re-rendering if its props haven't changed.
    - Code Splitting: Using React.lazy() and <Suspense> to load components only when they are needed.
    - Implement lazy loading for images using the loading="lazy" attribute or libraries.
    - Virtualization: Rendering only the visible items in a very long list to improve performance (e.g., using react-window).
    - React DevTools Profiler: Knowing how to use this tool to find and fix performance bottlenecks in your application.
- Testing:
    - Jest & Vitest: The leading test runners for JavaScript applications.
    - React Testing Library (RTL): The standard for testing React components. Its philosophy is to test your components in the way a user would interact with them.
    - Mocking: Using tools like Jest's mocking capabilities to fake API calls or functions during tests.
