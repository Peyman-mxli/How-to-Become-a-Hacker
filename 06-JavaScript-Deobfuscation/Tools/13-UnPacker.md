# UnPacker — Historical JavaScript Packer Analysis

## Purpose and architecture

Matthew Francis-Landau's historical browser utility attempts to reconstruct code produced by recognizable JavaScript packing formats. A packed sample commonly stores a transformed program, dictionary and reconstruction wrapper. Unpacking recovers text, whereas formatting improves presentation. Custom obfuscation may not be supported.

## Official access and downloads

- Original tool: https://matthewfl.com/unPacker.html
- Author explanation: https://matthewfl.com/19/old-blog/unpacker
- Additional explanation: https://matthewfl.com/10/old-blog/the-unpacker-again

The original tool page was reachable. No official standalone installer or supported local distribution was verified. No installation is needed for the hosted version. Its treatment of arbitrary input and privacy have not been independently audited; use local static analysis for unknown or sensitive material.

## Usage and original example

The HTB lesson instructs pasting a trusted packed example, removing leading blank lines and selecting UnPack. The old tool may expect the wrapper at the beginning. Inspect the reconstructed text; never assume the output is safe to execute.

Manual educational example, not actual UnPacker-generated output:
~~~text
2 0(){1("Demo");}
~~~
Dictionary: 0 = greet; 1 = console.log; 2 = function.
Recovered source:
~~~javascript
function greet(){console.log("Demo");}
~~~
The declaration itself produces no output. Calling greet would print Demo. This illustrates token substitution without running a wrapper.

The familiar eval(function(p,a,c,k,e,d) ...) pattern can indicate dictionary-based packing, but exact parameter roles vary. A typical wrapper substitutes tokens and returns source; eval then executes it. Replacing outer eval with logging still runs the wrapper and is not a security sandbox. Prefer static manual reconstruction or vetted local static tools.

## Troubleshooting and security

If the tool does not recognize the source, verify that the input is complete and the known wrapper is present; remove leading blank lines in a copy only. If the historical site fails, use local inspection or the supported unpacking features of js-beautify. Do not run truncated or unfamiliar wrappers. Do not paste lab flags, credentials or private project source into online tools. No installation, hosted unpacking output or runtime execution claimed.

UniTrack and ProTrack-MX must enforce sensitive decisions on the backend; recovered browser code can reveal client-side behavior but is not evidence of a vulnerability on its own.

Related: [JavaScript Beautifier](12-JavaScript-Beautifier.md) and [Section 6](../06-Deobfuscation.md).
