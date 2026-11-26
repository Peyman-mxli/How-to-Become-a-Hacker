# Study workflow and new-chat handoff

This is the English-only continuity guide for the independent HTB Academy study portfolio. The root README and each module README are the authoritative progress records. Read them before resuming; the checkpoint below is a snapshot, not a substitute for current status.

## Checkpoint — November 25, 2026

- Intro to Academy: 8/8 completed.
- Learning Process: 20/20 completed.
- Linux Fundamentals: 30/30 completed, confirmed by the learner's HTB dashboard. All numbered lesson notes and supplemental guides are in 03-Linux-Fundamentals/.
- Total: 4 completed modules, 66 confirmed completed sections; 73 studied and documented sections.
- Web Requests (Tier 0): 8/8 completed and documented, confirmed by the learner's HTB completion share preview. Section 1 download and flag extraction verified. Section 2 has no mandatory questions; learner advanced to Section 3. Section 3 request method identified and advertised Apache version verified using a GET request to the assigned lab. Learner advanced to Section 4; individual submission acceptance was not separately shown. Section 4 theory documented; mandatory Network request identification and flag extraction verified from learner screenshots; learner advanced to Section 5; individual submission acceptance not separately shown. Section 5 theory documented; no mandatory questions in supplied text. Learner advanced to Section 6; GET and Basic authentication documented, mandatory authenticated search and flag extraction verified. Learner advanced to Section 7; POST theory documented; mandatory valid login, session cookie and authenticated JSON POST flag retrieval verified. Learner advanced to Section 8; CRUD API theory documented; mandatory CRUD requests and final flag retrieval verified. Individual acceptance screens were not separately supplied; overall completion confirmed.
- Current module: Introduction to Web Applications (Tier 0), 7/17 full lessons studied and documented (Sections 1–3, 6–9); reached Section 9. Section 5 question accepted; full Sections 4–5 and Section 4 answer acceptance pending. Section 6 question area not supplied; learner advanced to Section 7. Section 7 theory documented; exposed-password retrieval verified from assigned lab HTML source; learner advanced to Section 8, with acceptance not separately shown. Section 8 HTML Injection theory and question reasoning documented; learner advanced to Section 9 without separate acceptance evidence. Section 9 XSS theory documented; local alert and cookie retrieval verified from learner screenshots; answer acceptance pending. Four individual JavaScript framework/library supplements are linked in the module index and Section 6 notes; these do not increase the HTB lesson count. Official course: https://academy.hackthebox.com/course/preview/introduction-to-web-applications
- Repository: https://github.com/Peyman-mxli/How-to-Become-a-Hacker
- Branch: main.

## Study rules

1. Explain every supplied lesson in English and Farsi in chat. Keep all GitHub files, headings, examples and commit messages in English only.
2. Follow the actual HTB section order and the learner-supplied content. Explain every major concept, command, option, output and relevant security implication. Label technical corrections and supplemental information clearly.
3. Give additional original examples for each lesson in GitHub, including illustrative scenarios, commands, expected output and defensive use cases as appropriate. Distinguish illustrative output from observed results. Include safe local examples or relevant software development analogies where useful.
4. No optional quizzes, knowledge reviews, unnecessary installations or optional labs unless specifically requested. Continue directly to the next section when there are no required questions.
5. Always present the exact submission answer at the very end of the chat response in a standalone copyable code block, preserving required capitalization, spacing and punctuation. If the answer requires evidence not yet supplied, state that it remains pending and give the next practical step instead of guessing. For required exercises, guide the learner step by step, one practical action at a time when appropriate. Explain the question, important keywords, command choice, syntax, options, actual output and answer extraction. Show the exact evidence line and field, explain how to isolate the requested value, and document sanitized evidence and extraction steps in GitHub. Wait for actual output when needed; do not invent lab results or claim answer acceptance without confirmation.
6. No icons, emojis or decorative symbols in chat responses or newly written notes.
7. Prioritize free tools and official documentation. Use the learner's Windows 11, PowerShell, Ubuntu WSL2 and Docker Desktop environment where relevant. Work only on owned or explicitly authorized systems and within the lab scope.
8. Keep credentials, VPN profiles, private keys, sensitive logs, student records and restricted assessment answers out of public GitHub. Write independent explanations, not copies of course text; follow HTB publication rules.

## GitHub procedure after each lesson

- Read the current repository and module indexes before writing.
- Create one numbered English Markdown file per section, following the existing naming style (for example, 03-Linux-Fundamentals/30-Shortcuts.md).
- For the current module, use 05-Introduction-to-Web-Applications/ and update its existing README after each lesson.
- Include title, concepts, command/parameter explanations where relevant, additional original examples, security considerations, relevant official references, practice and verification status.
- Document standalone required questions and their underlying concepts as well as full lessons, using independent examples and excluding restricted assessment answers. Link supplemental notes in the module index, and merge them into the numbered lesson when its full content is supplied.
- Add links to the module README and update the root README progress/checkpoint after every lesson. Record verified achievements in achievements/README.md only when evidence is supplied.
- For longer requested topics, create a separate English supplement in the relevant module folder and link it in the module index. Previous examples include Apache, VPN/OpenVPN, WSL setup, and network monitoring tools.
- Use the connected GitHub tools to commit to main directly, preserving existing content. Confirm successful writes and provide links. If access fails, disclose that instead of claiming success.
- Distinguish studied, documented, submitted, platform-completed and optional/not performed. Do not mark a module officially completed until the learner confirms it.

## Starting another chat

Paste this instruction:

Continue my HTB ethical hacking journey. Read STUDY-WORKFLOW.md and README.md in https://github.com/Peyman-mxli/How-to-Become-a-Hacker (main), then read the current module README and relevant prior lesson notes. Follow the documented study rules, explain every lesson in English and Farsi, and commit English-only notes with additional examples to GitHub after each lesson. No icons, emojis, review quizzes or unnecessary optional practice. Resume at the current checkpoint without repeating completed lessons.

As of this checkpoint, Web Requests is complete. Introduction to Web Applications Sections 1–3 are documented; a standalone HTML image-element question was covered in HTML-Image-Elements.md. Learner reached Section 5 and supplied a CSS text alignment question, documented in 05-CSS-Text-Alignment-Supplement.md. Section 5 question accepted after declaration formatting correction (learner confirmed). Full Sections 4–5 and Section 4 answer acceptance remain pending; Section 6 JavaScript theory documented. Section 7 theory documented and actual exposed password identified from the assigned target's HTML comment. Learner advanced to Section 8, without separate Section 7 acceptance evidence. Section 8 HTML Injection theory and question reasoning are documented; learner advanced to Section 9 without separate acceptance evidence. Section 9 XSS theory documented and cookie retrieval verified from the assigned lab's browser alert. Submit the extracted value privately, confirm acceptance and continue to Section 10. Public notes redact the Section 7 credential and target address. Do not count supplemental questions as complete lessons.
