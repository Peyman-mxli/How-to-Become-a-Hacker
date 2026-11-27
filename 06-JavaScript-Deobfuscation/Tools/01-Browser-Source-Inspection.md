# Browser Source Inspection — Developer Tools and cURL

## Definition and architecture

A browser downloads HTML and related resources, parses HTML into the Document Object Model (DOM), applies CSS, and executes JavaScript according to loading rules. Rendering and source inspection are different: the original response can differ from the live DOM. HTTP requests and responses cross a client/server boundary. Backend source and secrets should not be delivered to clients.

This guide covers Chrome DevTools, Firefox Developer Tools, View Page Source and cURL. All are available without payment; Firefox and cURL are open-source. Chrome DevTools is bundled with Chrome. No additional installation is necessary if the tools are already present.

## Installation and configuration

- Chrome: download from https://www.google.com/chrome/ and run the Windows installer if needed.
- Firefox: download from https://www.mozilla.org/firefox/new/ and run the installer. HTB Pwnbox may already provide Firefox.
- cURL: in Ubuntu WSL2, check `curl --version`. If missing, run `sudo apt update` and `sudo apt install curl`. Windows PowerShell can use `curl.exe --version` to explicitly invoke the cURL executable rather than a possible shell alias. Official downloads: https://curl.se/download.html.
- Use an ordinary browser profile for trusted local or authorized training applications. Inspect unknown scripts as text without execution; do not visit or run unknown malicious samples in a normal browser. Use an isolated analysis environment when required.

## Browser workflow

| Action | Chrome | Firefox |
| --- | --- | --- |
| View original page source | Ctrl+U | Ctrl+U |
| Open Developer Tools | F12 or Ctrl+Shift+I | F12 or Ctrl+Shift+I |
| Inspect current DOM | Elements | Inspector |
| Inspect loaded scripts | Sources | Debugger |
| Observe resource requests | Network | Network |

For a trusted local page, enter `view-source:http://127.0.0.1:5000/` into the address bar, or use Ctrl+U. Search with Ctrl+F for comments, script tags and stylesheet links. Click a referenced resource to view its source. The browser resolves relative URLs against the document base URL. Use Network to see request URLs and response metadata; reload an authorized page if a request was missed. Use Sources/Debugger to inspect scripts and pretty-print minified text. Pretty-printing changes presentation, not necessarily obfuscation. Do not paste unknown code into the Console.

## cURL command reference

```bash
curl --version
curl -sS 'http://127.0.0.1:5000/'
curl -i 'http://127.0.0.1:5000/'
curl -sS -o page.html 'http://127.0.0.1:5000/'
```

- --version reports the installed release.
- -s disables the progress meter and routine error messages; -S restores errors with -s.
- -i includes HTTP response headers with the body.
- -o page.html saves the response body to a local file.
- 127.0.0.1 is IPv4 loopback, 5000 is the port and / is the root path. These commands require an owned local server listening on that port.
- cURL retrieves responses but does not render HTML or execute JavaScript. It may return an HTTP error page as a body; inspect status headers when troubleshooting.

## Original example

An owned local page might return:

```html
<!DOCTYPE html>
<html>
<head><link rel="stylesheet" href="style.css"></head>
<body>
  <!-- Demo note: public example only -->
  <h1>Example</h1>
  <script src="app.js"></script>
</body>
</html>
```

Source view and cURL can reveal the comment and the two resource references. A browser may separately request style.css and app.js. The Elements/Inspector panel may display a different live DOM if the script modifies the page. All output here is illustrative; no local server or learner-side installation has been verified.

## Troubleshooting

| Symptom | Explanation and next check |
| --- | --- |
| Ctrl+U is unavailable | Use browser menu View Page Source, or cURL for an authorized URL. |
| Source differs from Elements | Scripts or parsing may modify the live DOM. Compare the original response. |
| Network list is empty | Open Network before reloading the authorized page. |
| cURL command not found | Check installation and PATH; use curl.exe on Windows if necessary. |
| Connection refused or timeout | Verify local service or assigned lab timer, address and authorized network access. |
| Script is minified | Pretty-print for readability; analyze semantics separately. |
| HTTP error page | Inspect response headers and status; do not assume success based on body alone. |

## Security and project applications

Use only owned or explicitly authorized targets. Never execute unknown scripts or publish private lab addresses, answers, credentials or student data. Review deployed HTML, CSS, JavaScript and source maps for accidental secrets. UniTrack must enforce QR and role checks on the Flask backend; ProTrack-MX must enforce record permissions through backend authorization and Supabase RLS. Hidden elements and obfuscation do not provide access control.

## Official references

- [Chrome DevTools](https://developer.chrome.com/docs/devtools/)
- [Firefox Developer Tools](https://firefox-source-docs.mozilla.org/devtools-user/)
- [MDN DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
- [cURL manual](https://curl.se/docs/manpage.html)
- [cURL downloads](https://curl.se/download.html)

Supplemental guide only; no extra HTB lesson credit, lab or installation claimed.
