# Lesson 30/30 — Shortcuts

## Purpose
Keyboard shortcuts make terminal navigation, editing, command recall and process management faster. These notes focus on typical interactive Bash/Readline Emacs-style bindings and terminal settings. Bindings may vary by shell, terminal emulator and operating system.

## Completion and cursor navigation
| Shortcut | Function |
| --- | --- |
| Tab | Complete commands, filenames, directory names or supported options |
| Tab twice | Typically show possible completions when ambiguous |
| Ctrl+A | Beginning of the line |
| Ctrl+E | End of the line |
| Ctrl+Left / Ctrl+Right | Backward / forward by word, depending on terminal bindings |
| Alt+B / Alt+F | Backward / forward by word |

Example: type cd /et then press Tab to complete /etc/ when unambiguous. Completion does not execute a command.

## Editing and restoring text
| Shortcut | Function |
| --- | --- |
| Ctrl+U | Kill text from cursor to beginning of line |
| Ctrl+K | Kill text from cursor to end of line |
| Ctrl+W | Kill the preceding word |
| Ctrl+Y | Yank recently killed text from the shell's kill ring |
| Ctrl+L | Clear/redraw the terminal display |

The kill ring is separate from the Windows clipboard. Ctrl+L and the clear command do not delete command history.

## Process control
| Shortcut | Function |
| --- | --- |
| Ctrl+C | Typically send SIGINT to the foreground process group |
| Ctrl+D | Signal EOF when the input buffer is empty; may exit the shell |
| Ctrl+Z | Typically send SIGTSTP to suspend the foreground job |

SIGINT normally interrupts a command, but a program can handle or ignore it. Ctrl+D is not a Unix signal; when editing a nonempty line, it may delete the character under the cursor. Ctrl+Z suspends a job rather than making it run in the background automatically.

Supplemental job-control commands:
~~~bash
jobs
bg %1
fg %1
~~~
jobs lists shell jobs; bg resumes a stopped job in the background; fg brings a job into the foreground. %1 refers to job number 1 and must be replaced with the actual job identifier. No commands executed in this lesson.

## Command history
Ctrl+R starts reverse incremental history search. Type a keyword to find an earlier command; Ctrl+R again searches older matches; Enter executes the selected command, while editing keys can return it for editing. Review any recalled command before execution. Up/Down arrows browse previous/next entries. The history command displays available shell history; never enter or publish credentials in command arguments.

## Desktop and terminal shortcuts
Alt+Tab switches applications in desktop environments. Ctrl+Plus and Ctrl+Minus commonly zoom terminal text in/out, depending on emulator configuration. In Windows Terminal, Ctrl+Shift+C / Ctrl+Shift+V commonly copy/paste, and zoom bindings may differ. Windows shortcuts and terminal-specific settings are distinct from Bash bindings.

## Completion status
All supplied 30 Linux Fundamentals lessons have now been studied and independently documented. Section 30 contains no mandatory question in the supplied text. No new practical execution was verified in this lesson. On November 24, 2026, the learner supplied an HTB dashboard screenshot showing Linux Fundamentals marked Completed. Module completion is confirmed; specific rewards and badges were not verified.

Study preference: explain lessons in English and Farsi in chat; maintain English-only GitHub notes; skip optional exercises and knowledge quizzes unless explicitly requested.

Independent English study notes based on the learner-provided HTB Academy Section 30, with supplemental technical clarifications.
