# Section 09 — Decoding

**Module:** JavaScript Deobfuscation | **Section:** 9/11 | **Date:** November 27, 2026

**Status:** Theory documented from learner-supplied HTB lesson. One mandatory question. The previous observed 32-character response was independently decoded as standard Base64; the decoded value and target are excluded from public documentation. The learner's POST response contained the assessment flag, verified from terminal evidence; submission acceptance remains pending. Displayed rewards +15 section and +30 question are not verified as earned.

## 1. Concepts

Encoding changes data representation according to defined rules; decoding reverses it. Encryption protects confidentiality using cryptographic algorithms and keys; hashing produces a digest and is not intended to be reversible; obfuscation reduces readability. Encoding is not encryption. The supplied HTB lesson introduces Base64, hexadecimal and ROT13, in that order. Encoded data may appear in obfuscated scripts and be decoded during execution. Treat appearance as a clue, not proof.

## 2. Base64

Standard alphabet: uppercase A-Z, lowercase a-z, digits 0-9, plus and slash; equals is padding, not one of the 64 data symbols. Three bytes (24 bits) map to four six-bit indices. For ASCII Cat, bytes 01000011 01100001 01110100 split into 010000 110110 000101 110100, indices 16, 54, 5, 52, giving Q2F0. Standard padded Base64 length is a multiple of four. Padding is absent when the input contains complete three-byte groups; some variants omit padding. Base64URL uses hyphen and underscore in place of plus and slash. Binary output need not be readable text. Encoding increases size and does not hide secrets.

GNU Base64 is supplied by Coreutils. Official links: https://www.gnu.org/software/coreutils/ ; https://ftp.gnu.org/gnu/coreutils/ ; https://www.gnu.org/software/coreutils/manual/coreutils.html. Ubuntu WSL2: base64 --version; if missing, sudo apt update and sudo apt install coreutils. No new installation is required in the existing environment if already available.

Original examples:
~~~bash
printf '%s' 'Hello World' | base64
printf '%s' 'SGVsbG8gV29ybGQ=' | base64 -d
~~~
Expected output respectively SGVsbG8gV29ybGQ= and Hello World. printf '%s' emits exactly the given string; pipe transfers stdout to stdin; -d decodes; GNU -w 0 disables output wrapping. HTB uses echo, which normally appends a newline. Hello without newline encodes to SGVsbG8=; Hello with newline encodes to SGVsbG8K. HTB's URL example includes a newline. See [Base64 guide](Tools/16-Base64.md).

## 3. Hexadecimal

Hex is base 16; digits 0–9 and A–F/a–f. Each digit represents four bits; two digits represent a byte. ASCII A/B/C = 41/42/43 hex, a/b/c = 61/62/63, space = 20, newline = 0a. ASCII is one byte per character, whereas UTF-8 can use multiple bytes per character. Plain byte hex normally has an even count of digits. Hex is a representation, not confidentiality.

xxd creates and reverses hexdumps. Check xxd -v; if missing in Ubuntu, sudo apt update and sudo apt install xxd. Official Vim source: https://github.com/vim/vim ; manual https://github.com/vim/vim/blob/master/runtime/doc/xxd.1. man ascii displays the ASCII table if installed; Ubuntu manpages may be needed.

~~~bash
printf '%s' 'Hello World' | xxd -p
printf '%s' '48656c6c6f20576f726c64' | xxd -p -r
~~~
Expected output: 48656c6c6f20576f726c64 and Hello World. -p is plain hex and -r reverses it; -v displays version. See [xxd guide](Tools/17-xxd.md).

## 4. Caesar cipher and ROT13

Caesar shifts alphabetic letters by a fixed number, wrapping at the end. ROT13 uses 13 positions: A–M map to N–Z and vice versa, with lowercase mapped separately. Applying ROT13 twice restores the original because 13 + 13 = 26. Digits and punctuation are unchanged. The supplied URL example has http://www mapped to uggc://jjj. ROT13 has no meaningful modern cryptographic security.

GNU tr translates sets of characters; it is part of Coreutils. Check tr --version; install Coreutils only if absent. Official manual: https://www.gnu.org/software/coreutils/manual/coreutils.html.

~~~bash
printf '%s' 'Hello World' | LC_ALL=C tr 'A-Za-z' 'N-ZA-Mn-za-m'
printf '%s' 'Uryyb Jbeyq' | LC_ALL=C tr 'A-Za-z' 'N-ZA-Mn-za-m'
~~~
Expected outputs Uryyb Jbeyq and Hello World. LC_ALL=C makes ASCII range behavior predictable. The source alphabet is uppercase/lowercase letters; the destination rotates them. See [tr guide](Tools/18-tr-ROT13.md). Browser alternative https://rot13.com/ requires JavaScript; its interactive behavior was not independently tested. See [ROT13 browser guide](Tools/19-ROT13-Online.md).

## 5. Identify unknown encodings

Base64 often uses mixed-case letters, digits, plus/slash and possibly equals; hex uses only hex digits, ordinarily in byte pairs; ROT13 preserves punctuation and letter patterns. These clues are not definitive. Identify possible representations, decode as data, inspect output, and verify by re-encoding where appropriate. Do not execute decoded scripts. The supplied example response in the lesson is a demonstration reminder, not the learner's observed Section 8 response.

The dCode Cipher Identifier is an online tool that suggests encodings/ciphers using patterns and statistical clues; results are hypotheses. Its source and offline distribution are not publicly offered by its provider. Use non-sensitive examples only. Official https://www.dcode.fr/cipher-identifier. See [Cipher Identifier guide](Tools/20-Cipher-Identifier.md). CyberChef is a supplemental open-source alternative for local/browser decoding and candidate identification, not a named tool in this lesson: https://gchq.github.io/CyberChef/ ; https://github.com/gchq/CyberChef/releases. See [CyberChef guide](Tools/21-CyberChef.md). Short strings can match several representations. Strong encryption may require an authorized key and cannot be treated as encoding.

## 6. Mandatory lab methodology

Question: identify and decode the response from the previous exercise, then send a POST to /serial.php with serial=YOUR_DECODED_OUTPUT to obtain the flag. The learner's actual prior response was 32 characters, consistent with Base64. A strict local standard Base64 decode of that data succeeded and produced readable 24-byte ASCII text containing leetspeak and underscores. No JavaScript execution or further live request by the assistant was performed. The exact encoded and decoded assessment values are redacted.

Sanitized reference:
~~~bash
printf '%s' 'ENCODED_RESPONSE' | base64 -d
curl -sS -X POST -d "serial=DECODED_OUTPUT" http://SERVER_IP:PORT/serial.php
~~~
Use the actual decoded value, not the human-readable translation. -d supplies the form field, and normally selects POST; -X POST explicitly states the method. The learner subsequently supplied terminal evidence showing the flag returned by the POST request. Avoid unnecessary repeat POSTs, and if the lab expires, retrieve the new assigned response rather than assuming it is unchanged. No target address or assessment answer published.

## 7. Defensive implications

UniTrack: encoded QR payloads are representations, not proof of authenticity; enforce QR validity, role checks and visitor expiration server-side. ProTrack-MX: Base64 or hex does not protect student data or privileged keys; use backend authorization, Supabase RLS, TLS and appropriate secure credential storage. Analyze encoded data as data, not executable code; do not paste secrets into online tools. These are recommendations, not new audit findings.

## References

- https://academy.hackthebox.com/course/preview/javascript-deobfuscation
- https://www.gnu.org/software/coreutils/manual/coreutils.html
- https://github.com/vim/vim/blob/master/runtime/doc/xxd.1
- https://rot13.com/
- https://www.dcode.fr/cipher-identifier
- https://github.com/gchq/CyberChef
- https://curl.se/docs/manpage.html

Next action: submit the observed flag and proceed to Section 10 Skills Assessment after acceptance. Section 8 acceptance not separately supplied; Section 9 platform completion and rewards pending.

## Verified practical exercise evidence

The learner used Ubuntu WSL2 and cURL to submit the decoded serial to the assigned training endpoint. The original 32-character Base64 response and decoded 24-byte ASCII value were verified in the study conversation. Sanitized reproduction:

~~~bash
printf '%s' 'ENCODED_RESPONSE' | base64 -d
curl -sS -X POST -d "serial=DECODED_OUTPUT" http://SERVER_IP:PORT/serial.php
~~~

The actual learner terminal output showed an HTB-format flag directly followed by the shell prompt. The prompt is not part of the response. The assessment flag and both serial representations are deliberately omitted from public notes. The observed flag uses leetspeak; digits represent letters, and its exact wording is excluded to avoid publishing an answer key. The request was made by the learner, not the assistant. No response status code was captured and no vulnerability is established by this exercise alone.

Workflow verified: inspect source, recover readable JavaScript, identify the endpoint and HTTP method, retrieve the encoded response, identify Base64, decode it as data, and submit the decoded serial as the specified form field. The returned flag confirms the intended training functionality. No execution of the original obfuscated wrapper was required. HTB answer acceptance, rewards and platform completion are still unverified.
