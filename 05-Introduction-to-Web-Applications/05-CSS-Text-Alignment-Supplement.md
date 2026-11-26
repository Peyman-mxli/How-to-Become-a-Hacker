# Section 05 — CSS text alignment supplemental notes

## Concept
The CSS text-align property controls horizontal alignment of inline content within a block container. The physical value left aligns content against the left edge. Other common values are right, center, justify, start and end. The logical values start and end respect writing direction, making them useful in multilingual interfaces.

## Original examples

```css
.student-name {
    text-align: left;
}
.report-title {
    text-align: center;
}
.amount {
    text-align: right;
}
.description {
    text-align: start;
}
```

For a multilingual application, the HTML dir attribute can specify text direction, while text-align: start follows the writing direction. CSS alignment changes inline content placement; it does not reposition the block itself or enforce access control.

## Security considerations
CSS is presentation, not authorization. Hiding or styling a protected control is not a substitute for server-side authorization. Never include private data in a page solely because CSS makes it invisible.

## Verification
The learner supplied a standalone required CSS knowledge question identified as Section 5/17. The underlying concept was explained; no Pwnbox or live lab required. Full lesson content and answer acceptance have not been supplied. Examples are illustrative and were not executed. Section 4's full lesson also remains pending. No additional full section completion is claimed.

Reference: [MDN — text-align](https://developer.mozilla.org/en-US/docs/Web/CSS/text-align).
