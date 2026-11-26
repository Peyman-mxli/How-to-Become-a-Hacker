# HTML image elements — supplemental notes

## Concept
The HTML `img` element embeds an image in a document. It is a void element: it does not require a closing tag. The `src` attribute identifies the image resource, and `alt` provides a text alternative for accessibility or when the image cannot be displayed.

## Original examples

```html
<img src="/static/images/school-logo.png" alt="School logo" width="120" height="120">
```

A responsive image in a school dashboard:

```html
<img class="profile-photo" src="/static/images/demo-avatar.png" alt="Example profile photo" loading="lazy">
```

```css
.profile-photo {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
}
```

The `loading` attribute can defer loading off-screen images. CSS controls presentation, while HTML defines the embedded content. Use an empty alt attribute for purely decorative images when appropriate. The browser normally makes a resource request to retrieve an image referenced by a URL. Avoid untrusted image URLs where unnecessary, never embed secrets in URLs, and enforce access controls for private images on the server. Use synthetic assets in public examples.

## Verification
The learner supplied a standalone HTML knowledge question. The concept was explained from standard HTML behavior; no live lab, Pwnbox, or command execution was required. The complete lesson title, section number, and platform acceptance have not been independently confirmed. This supplemental note does not reproduce the assessment question or mark another full section complete.

Reference: [MDN — The Image Embed element](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/img).
