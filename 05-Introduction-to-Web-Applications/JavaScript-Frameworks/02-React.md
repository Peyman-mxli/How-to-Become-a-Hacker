# 02 — React

## What is React?
React is an open-source JavaScript library for building component-based user interfaces, originally developed at Facebook (now Meta). It focuses on the UI rather than supplying every application concern. It can be used for web interfaces and, through React Native, native mobile applications. React is not a database or a backend service.

## How React works
Components describe UI as a function of props and state. JSX allows HTML-like markup in JavaScript or TypeScript. React renders components, reconciles changes and updates the DOM as necessary. Props are inputs supplied by a parent; state is component memory that can change over time. Hooks such as useState manage state; useEffect synchronizes with external systems when needed. Routing and data fetching may be provided by additional libraries or application frameworks.

| Concept | Purpose |
| --- | --- |
| Component | Reusable UI unit |
| JSX | HTML-like syntax in JavaScript |
| Props | Inputs to a component |
| State | Mutable-in-time component data |
| useState | Declare and update local state |
| Event handler | Respond to interaction |
| Reconciliation | Determine how to update the UI |

## Original example: attendance counter
Illustrative component in a React project:

~~~jsx
import { useState } from 'react';

export default function AttendanceCounter() {
  const [present, setPresent] = useState(0);
  return (
    <section>
      <h2>Attendance demo</h2>
      <p>Present: {present}</p>
      <button type="button" onClick={() => setPresent(n => n + 1)}>
        Add demo record
      </button>
    </section>
  );
}
~~~

useState(0) returns the current value and a setter. The event handler passes an updater function to setPresent. React re-renders the component and displays the new value inside JSX braces. Expected: Present: 0, then Present: 1 after one click. Import and render this component in an existing React application. No server request or database write occurs.

## When to use React
Useful for interactive dashboards, reusable components, messaging interfaces and applications with frequently changing UI state. It provides flexibility, but teams must choose supporting routing, state and data-fetching patterns where needed.

## Connection to ProTrack-MX
A React-based school application can represent chat messages, group summaries and attendance tables as components. UI state might track selected classes and loading status. Real persistence and access controls must be enforced by backend services or database policies. This is a conceptual connection, not a source-code audit.

## Security for developers and authorized testers
Ordinary JSX text expressions are escaped; avoid rendering untrusted HTML with dangerouslySetInnerHTML. Never embed privileged service credentials in browser bundles or assume that environment variables used in frontend builds are secret. Hidden buttons do not protect APIs. Verify server-side object and role authorization using authorized test identities. Audit dependencies and treat user-provided URLs and content carefully.

## References
- https://react.dev/learn
- https://react.dev/learn/responding-to-events
- https://react.dev/learn/state-a-components-memory
- https://react.dev/reference/react-dom/components/common

Original supplemental guide. Examples are illustrative and not executed; no installation required.
