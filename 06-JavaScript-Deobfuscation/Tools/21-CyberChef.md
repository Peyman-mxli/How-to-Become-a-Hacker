# CyberChef — Supplemental Open-Source Data Analysis Tool

CyberChef is an open-source GCHQ web application for transforming and inspecting data. It uses a recipe of operations on input and presents output. Its upstream documentation states the official app processes input in the browser; use an authentic downloaded copy for offline private analysis. This is supplemental, not a named tool in the supplied HTB section.

## Official access and downloads
- https://gchq.github.io/CyberChef/
- https://github.com/gchq/CyberChef
- https://github.com/gchq/CyberChef/releases
- https://github.com/gchq/CyberChef/wiki/Automatic-detection-of-encoded-data-using-CyberChef-Magic

Use the official hosted app or download a packaged release from its official release assets, extract it and open the included HTML in a modern browser following the release instructions. No separate Windows installer needed. Exact current asset name was not inspected. Do not download from unofficial mirrors.

## Examples
Enter SGVsbG8gV29ybGQ= in Input, search for From Base64, drag it into Recipe and inspect Output. Expected: Hello World. Enter 48656c6c6f20576f726c64 with From Hex and select the appropriate delimiter option (none for this continuous hex string); expected Hello World. Enter Uryyb Jbeyq with ROT13, expected Hello World. Clear the recipe between examples. To explore possible encodings, try Magic on non-sensitive demo data; verify candidates rather than treating automatic guesses as proof. These are reference steps, not observed tool output.

## Troubleshooting and security
If output is wrong, check recipe order, input encoding, alphabet and delimiters. If output is binary, view it as data rather than execute it. Use official releases and keep confidential data out of shared URLs and recipes. Base64, hex and ROT13 do not provide authorization or confidentiality. No installation or live conversion claimed.
