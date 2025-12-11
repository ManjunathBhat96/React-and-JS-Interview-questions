React Hooks — Where to Use & Where NOT to Use
1️⃣ useState
✅ USE when:

You need local, interactive state

UI changes based on user actions

Form inputs

Toggle values

Temporary state (show/hide modal, dropdown)

Simple, independent pieces of state

❌ DO NOT USE when:

Derivable values (e.g., total = price * qty)

State depends on previous props

State is global/shared → useContext/useReducer instead

You need complex state transitions → prefer useReducer

2️⃣ useEffect
✅ USE when:

You need to run side effects after render

API calls

Event listeners (scroll, resize)

Timers (setInterval, setTimeout)

Subscriptions (websocket)

Syncing state with localStorage

Cleanup actions (return function)

❌ DO NOT USE when:

Calculating values → use variables/useMemo instead

Setting state unnecessarily → causes infinite loops

Logic can run inside event handlers

Deriving state from props

Updating state JUST after render (wrong pattern)

3️⃣ useContext
✅ USE when:

You want to avoid prop drilling

Theme, Auth, User, Language

Small–medium global state

A few components need shared data

❌ DO NOT USE when:

Large, complex global state → use Redux or Zustand

You need high performance (large context triggers re-renders)

You want fine-grained updates → use state libraries instead

4️⃣ useReducer
✅ USE when:

State transitions are complex

Many related values (forms, nested state)

Logic contains multiple steps or conditions

You want Redux-like state management locally

❌ DO NOT USE when:

Simple toggles or small independent states

One-line setState logic

State is not related → use multiple useState instead

5️⃣ useMemo
✅ USE when:

You have expensive calculations

Filtering large arrays

Heavy loops/computations

Derived values passed to memoized children

You need stable references for arrays/objects

Prevent unnecessary re-renders

❌ DO NOT USE when:

Calculations are small/lightweight

It doesn’t solve a real performance issue

You memoize simple values (waste)

You think “useMemo always improves performance” (wrong)

6️⃣ useCallback
✅ USE when:

Passing functions to memoized children (React.memo)

Functions used inside useEffect/useMemo dependency arrays

Prevent re-renders caused by new function references

Stable function identity is required

❌ DO NOT USE when:

Not using React.memo

Function is simple/no heavy logic

Function not part of dependencies

Using just for “optimization” → adds overhead

7️⃣ useRef
✅ USE when:

Accessing DOM elements

Storing values that don’t cause re-render

Use as instance variable (like class this)

Storing timers, previous values, counters

Keeping stable mutable values

❌ DO NOT USE when:

You expect re-renders when value changes

Storing derived values (better use state)

Storing UI data that should trigger updates

8️⃣ useLayoutEffect
✅ USE when:

You need to run effect before browser paints UI

Measuring DOM size/position

Synchronous layout reading/writing (rare cases)

Fixing visual flickering issues

❌ DO NOT USE when:

You are doing data fetching

You don't need sync DOM measurements

Normal useEffect is enough

It blocks rendering → harms performance

9️⃣ useImperativeHandle
✅ USE when:

Customizing refs exposed to parent components

Building reusable component libraries

Allow parent to control child functions (play, focus, scroll)

❌ DO NOT USE when:

Normal props can solve the problem

Child doesn’t need to expose internal methods

🔟 useId
✅ USE when:

Unique IDs needed for inputs

Accessibility features

Server + Client consistency

❌ DO NOT USE when:

You need random IDs on every render

IDs depend on state or props