# 01 — Angular

## What is Angular?
Angular is an open-source application framework maintained by Google and its community. It provides a coordinated set of tools for creating substantial web applications. Modern Angular development commonly uses TypeScript, which adds static type checking and compiles to JavaScript. Angular is distinct from the older AngularJS 1.x framework.

## How Angular works
A component combines a TypeScript class, an HTML template and optional styles. Angular connects template expressions to component data and updates the interface when relevant state changes. Services encapsulate shared logic. Dependency injection supplies services to components rather than requiring each component to construct its own dependencies. The router maps URLs to views; forms provide input management and validation; HttpClient communicates with HTTP APIs. Signals represent reactive values, while RxJS observables are commonly used for asynchronous streams.

## Core concepts
| Concept | Purpose |
| --- | --- |
| Component | Encapsulate a section of the interface |
| Template | Describe rendered HTML |
| Interpolation | Display values using double braces |
| Event binding | Connect an event to an action |
| Signal | Track reactive state |
| Service | Share application logic |
| Dependency injection | Supply dependencies |
| Router | Navigate between views |
| HttpClient | Communicate with HTTP services |

## Original example: attendance counter
Illustrative standalone component in a modern Angular project, saved as attendance-counter.component.ts:

~~~typescript
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-attendance-counter',
  standalone: true,
  template: `
    <h2>Attendance demo</h2>
    <p>Present: {{ present() }}</p>
    <button type="button" (click)="markPresent()">Add demo record</button>
  `
})
export class AttendanceCounterComponent {
  present = signal(0);

  markPresent() {
    this.present.update(value => value + 1);
  }
}
~~~

Import the component into a suitable parent component and include its selector in the parent's template. The Component decorator registers metadata; signal(0) sets initial state; present() reads it; interpolation displays it; (click) connects the event; update increments the value. Expected behavior: Present: 0 initially, then Present: 1 after one click. This example is local UI state, not a persistent attendance record.

## When to use Angular
Suitable for complex dashboards, multi-team applications and projects that benefit from conventions, integrated routing and dependency injection. The trade-off is a larger conceptual learning curve and potentially unnecessary complexity for a tiny page. A hypothetical school portal could separate classes, attendance and reports into components and services.

## Security for developers and authorized testers
Angular escapes ordinary template interpolation and applies context-dependent sanitization to certain bindings. Avoid bypassing sanitization with trust-bypass APIs unless the value has been carefully reviewed. Route guards improve navigation behavior but cannot replace backend authorization. Keep private keys on the server; inspect dependencies and configuration. For an authorized review, trace component actions to API requests and verify that permissions are enforced independently of the UI. No live application was assessed.

## References
- https://angular.dev/essentials/components
- https://angular.dev/essentials/signals
- https://angular.dev/guide/security
- https://angular.dev/guide/http

Original supplemental guide. Examples are illustrative and not executed; no installation required.
