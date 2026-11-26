# 04 — jQuery

## What is jQuery?
jQuery is an open-source JavaScript library designed to simplify DOM selection, manipulation, events, AJAX requests and common browser interactions. It is not a complete application framework. It remains relevant when maintaining existing websites and server-rendered applications. Modern browser APIs can perform many of these tasks without an additional library.

## How jQuery works
The jQuery function (often written as $) accepts selectors or elements and returns a collection with methods that can be chained. Unlike the component-and-state approach used by Angular, React and Vue, typical jQuery code directly changes DOM elements in response to events.

| Concept | Purpose |
| --- | --- |
| $() | Select or wrap DOM elements |
| .on() | Register an event handler |
| .text() | Read or set plain text |
| .html() | Read or set interpreted HTML |
| .addClass() | Change CSS classes |
| .val() | Read or set form values |
| $.ajax() | Send an HTTP request |

## Original example: attendance counter
Illustrative HTML page with a trusted, locally supplied jQuery file:

~~~html
<h2>Attendance demo</h2>
<p id="status">Present: 0</p>
<button id="check" type="button">Add demo record</button>
<script src="./jquery.min.js"></script>
<script>
$(function () {
  let present = 0;
  $('#check').on('click', function () {
    present += 1;
    $('#status').text('Present: ' + present);
  });
});
</script>
~~~

The ready handler runs when the document is ready; #check selects an element by ID; .on registers a click handler; .text updates the paragraph safely as plain text. Expected: Present: 0, then Present: 1 after one click. The example assumes the jQuery resource is already available. No dependency installation is required for reading this guide.

## AJAX example
For a hypothetical same-origin endpoint on an owned application:

~~~javascript
$.ajax({
  url: '/api/demo/summary',
  method: 'GET',
  dataType: 'json'
}).done(function (data) {
  $('#status').text('Demo records: ' + String(data.count));
}).fail(function () {
  $('#status').text('Unable to load summary');
});
~~~

dataType specifies expected response parsing, done handles success and fail handles failure. This endpoint is illustrative and was not contacted.

## When to use jQuery
Useful for existing server-rendered sites, legacy plugins and small DOM enhancements when jQuery is already included. For a new lightweight page, vanilla JavaScript may be simpler. For a complex stateful application, a component-oriented architecture can improve maintainability.

## Security for developers and authorized testers
Use .text() for untrusted plain text rather than .html(). Avoid inserting untrusted markup and avoid dynamic script execution. Review plugins and outdated jQuery versions, load dependencies from trusted sources, and enforce backend authorization regardless of browser-side checks. No vulnerabilities are claimed for any specific application.

## References
- https://learn.jquery.com/
- https://learn.jquery.com/events/event-basics/
- https://learn.jquery.com/ajax/
- https://api.jquery.com/text/
- https://api.jquery.com/html/

Original supplemental guide. Examples are illustrative and not executed; no installation required.
