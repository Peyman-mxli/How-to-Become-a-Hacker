# Lesson 09/30 — Editing Files

## Overview
Linux supports terminal-based text editing. Nano is approachable for beginners, while Vim offers modal editing and integration with specialized Unix tools.

## Nano — why and how

~~~bash
nano notes.txt
~~~

nano launches the editor and notes.txt specifies the file. If the file does not exist, it is created when saved. Use a disposable practice file rather than editing system configuration.

1. Enter a line of practice text.
2. Press Ctrl+O to write the file.
3. Press Enter to confirm the filename.
4. Press Ctrl+X to exit.
5. Run cat notes.txt to verify the saved content.

The cat command prints file contents to standard output. This verifies the saved text rather than merely assuming the editor wrote it.

### Shortcuts
| Shortcut | Purpose |
| --- | --- |
| Ctrl+G | Help |
| Ctrl+O | Write file |
| Ctrl+X | Exit |
| Ctrl+W | Search |
| Ctrl+K | Cut |
| Ctrl+U | Paste previously cut text |
| Ctrl+Backslash | Replace |
| Alt+U | Undo |
| Alt+E | Redo |

The caret in Nano shortcuts means Ctrl; M usually means Alt. Search with Ctrl+W, enter a keyword and press Enter. Repeat the search to locate another occurrence.

## Important Linux account files
/etc/passwd contains account information including usernames, UIDs, GIDs, home directories and configured shells. Password hashes are normally stored separately in /etc/shadow, which has stricter permissions. Incorrect permissions on sensitive files can create security risks. Inspect only authorized systems and never publish password hashes or credentials.

## Vim
Vim is an open-source modal text editor derived from Vi. Different modes distinguish editing commands from text entry.

| Mode | Purpose |
| --- | --- |
| Normal | Editor commands |
| Insert | Text entry |
| Visual | Text selection |
| Command-line | Enter editor commands |
| Replace | Overwrite existing characters |
| Ex | Execute sequential editor commands |

### Supplemental practice — why and how

~~~bash
vim notes.txt
~~~

Press i to enter Insert mode, enter practice text, then press Esc to return to Normal mode. Type :wq and press Enter to save and exit. Use :w to save, :q to quit when possible, or :q! to discard unsaved changes intentionally. Verify with cat notes.txt.

## Optional exercise

~~~bash
vimtutor
~~~

This launches an interactive tutorial, normally using a practice copy. The lesson estimates 25–30 minutes. Practice inside the tutor rather than merely reading it. The command may be unavailable if Vim's tutorial package is not installed.

## Verification
No mandatory answer fields appear in the supplied lesson. Nano creation, editing, saving and verification were completed successfully. Vim and optional tutor execution remain unverified.

Source: [HTB Academy](https://academy.hackthebox.com/). Independent English study notes.

## Troubleshooting — No such file or directory

Actual lab attempt:
~~~bash
cat notes.txt
~~~

Observed output:
~~~text
cat: notes.txt: No such file or directory
~~~

### Why this happened
cat reads existing files; it does not create them. The relative path notes.txt refers to a file in the current working directory. The file could be absent or located elsewhere.

### How to resolve it
1. Run nano notes.txt to open a new practice file.
2. Enter practice text.
3. Press Ctrl+O, Enter, then Ctrl+X to save and exit.
4. Run cat notes.txt to verify the contents.

Alternative: use pwd to check the current directory and ls -l notes.txt to check whether the file exists. touch notes.txt can create an empty file if absent, but it does not add text.

The original error was observed in the lab. Successful creation and verification confirmed by the learner's terminal output.

## Verified Nano practice result

After resolving the missing file error, the learner opened nano notes.txt, entered text, saved the file and exited. Verification:

~~~bash
cat notes.txt
~~~

Actual output:

~~~text
I am learning Linux and Ethical Hacking with Hack The Box Academy.
~~~

Why this works: Nano writes the text to disk when saved, and cat subsequently reads the saved file. The successful output verifies that the missing-file error was resolved. No mandatory questions are present in the supplied section; VimTutor remains optional.
