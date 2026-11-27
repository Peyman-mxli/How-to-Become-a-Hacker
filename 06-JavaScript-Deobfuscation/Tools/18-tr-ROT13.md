# GNU tr — Character Translation and ROT13

tr translates, deletes or squeezes characters from input. ROT13 is a Caesar shift of 13 letters. Mapping the two alphabet halves twice restores the original; digits and punctuation are unchanged in the ASCII example. This is not modern encryption.

## Official downloads and installation
- https://www.gnu.org/software/coreutils/
- https://ftp.gnu.org/gnu/coreutils/
- https://www.gnu.org/software/coreutils/manual/coreutils.html

On Ubuntu WSL2 check tr --version; if missing, install coreutils with sudo apt update and sudo apt install coreutils. Windows users can use WSL2. Local documentation: man tr.

## Original examples
~~~bash
printf '%s' 'Hello World' | LC_ALL=C tr 'A-Za-z' 'N-ZA-Mn-za-m'
printf '%s' 'Uryyb Jbeyq' | LC_ALL=C tr 'A-Za-z' 'N-ZA-Mn-za-m'
printf '%s' 'abc' | LC_ALL=C tr 'a-z' 'A-Z'
~~~
Expected Uryyb Jbeyq, Hello World, ABC. LC_ALL=C ensures predictable ASCII range handling; tr maps positional characters from the first set to the second. The uppercase destination is N-Z then A-M; lowercase is n-z then a-m.

## Troubleshooting and security
If result differs, verify quoting, source/destination ordering, locale and whether text contains non-ASCII letters. ROT13 is not a general Unicode shift. Do not treat a plausible decode as definitive; applying the same operation twice checks reversibility, not the original encoding hypothesis. Avoid using obscurity for application credentials. No actual learner installation or execution claimed.
