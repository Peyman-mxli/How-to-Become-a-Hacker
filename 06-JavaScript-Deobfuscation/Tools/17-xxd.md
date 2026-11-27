# xxd — Hexadecimal Dumps

xxd displays byte data in hexadecimal, optionally with offsets and printable characters, and can reconstruct bytes from compatible dumps. Each byte uses two hex digits; ASCII A is 41, a is 61; UTF-8 may contain multiple bytes per character.

## Official access and installation
- https://github.com/vim/vim
- https://github.com/vim/vim/blob/master/runtime/doc/xxd.1
- https://packages.ubuntu.com/

On Ubuntu WSL2 check xxd -v; if missing run sudo apt update and sudo apt install xxd. Windows users can use WSL2; Vim's official download page is https://www.vim.org/download.php. No separate Windows installer was verified. man ascii displays the ASCII table if installed; man xxd displays local documentation.

## Examples
~~~bash
printf '%s' 'Hello World' | xxd -p
printf '%s' '48656c6c6f20576f726c64' | xxd -p -r
printf '%s' 'ABC' | xxd
~~~
Expected first two outputs are 48656c6c6f20576f726c64 and Hello World. The third shows a conventional dump containing bytes 41 42 43. -p selects plain hex; -r reverses; -v displays version. xxd -p input.bin > output.hex and xxd -p -r output.hex > restored.bin illustrate local file conversion; avoid overwriting originals. Use cmp to verify restored bytes.

## Troubleshooting and security
Verify even hex length and valid digits; xxd reverse mode may silently skip malformed content, so validate input independently for strict tasks. Check newline 0a and encoding if expected text differs. Hex is not encryption. Use static data inspection for authorized artifacts and keep private project data out of public repositories. No actual learner installation or execution claimed.
