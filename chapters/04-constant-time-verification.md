# Constant-Time Verification

A cryptographic function can compute exactly the right answer and still give away its key. If execution time, memory access pattern or branch behaviour depends on secret data, an attacker who can measure time (over a network, or from a process on the same machine) can learn about the secret. "Constant-time" code avoids secret-dependent branches, secret-dependent memory addresses and variable-time instructions on secret data. This chapter covers tools that test or verify that property.

## What constant time means

The usual rules for code handling secrets:

1. No branches whose condition depends on a secret.
2. No memory accesses whose address depends on a secret (this rules out naive table lookups such as classic AES T-tables).
3. No variable-time instructions on secrets (on some processors, division and some multiplications vary in time with operand values).
4. No early exits from comparisons (use constant-time equality for MACs and tags).

Compilers complicate this. Source code that looks branch-free can be compiled into a branch, and an optimiser can recognise a hand-written constant-time selection and turn it back into a conditional jump. That is why checking compiled binaries matters.

## Tool families

| Approach | Tools | What it checks | Strength | Weakness |
|---|---|---|---|---|
| Statistical timing tests | dudect | timing distributions for two classes of inputs differ? | black box, works on any code | statistical, can miss small leaks, sensitive to noise |
| Dynamic taint via memcheck | ctgrind idea, Valgrind with secrets marked undefined, TIMECOP in SUPERCOP | branches and addresses depending on secret bytes on executed paths | precise on executed paths, finds the exact line | only covers paths actually executed |
| MemorySanitizer-based | MSan with secrets poisoned | similar, at compiler instrumentation level | fast | needs full MSan-instrumented build |
| Static / formal | ct-verif, Binsec/Rel, others | all paths, on binary or IR | proofs | setup cost, scalability |
| Language-level | secret types (e.g. HACL*'s secret integers, Jasmin) | type system prevents misuse | by construction | requires writing code in that language |

## dudect

dudect comes from Oscar Reparaz, Josep Balasch and Ingrid Verbauwhede, "Dude, is my code constant time?" (DATE 2017). It runs the target many times on two classes of inputs, usually a fixed input versus random inputs, measures cycle counts, and applies Welch's t-test. A large t-statistic suggests that timing depends on the input class.

Interpret results carefully. A high t-value is evidence of a leak; a low one after a short run is weak evidence of absence. Run long, pin to a CPU core, disable frequency scaling where possible, and repeat.

## Valgrind-based checking

The idea popularised by Adam Langley's ctgrind is simple: tell Valgrind's memcheck that secret bytes are *uninitialised*. Memcheck already reports when a branch or memory address depends on uninitialised data. So any report is a place where control flow or addressing depends on the secret. Modern Valgrind does not need a patch; the client request macros are enough:

```c
#include <valgrind/memcheck.h>

uint8_t key[32];             // load a real key
VALGRIND_MAKE_MEM_UNDEFINED(key, sizeof key);   // mark as secret
compute_mac(out, key, msg, msg_len);
VALGRIND_MAKE_MEM_DEFINED(out, 16);             // output is public by design
```

Run under `valgrind ./test`. Reports such as "Conditional jump or move depends on uninitialised value(s)" point at secret-dependent branches; "Use of uninitialised value" in address computation points at secret-dependent memory access. Some libraries build this into their test suites: BoringSSL has a constant-time validation build option that uses this technique, and TIMECOP applies it to implementations submitted to the SUPERCOP benchmarking suite.

Remember to mark *declassified* values as defined. Public outputs computed from secrets (a signature, a ciphertext) are fine to branch on, and a tool cannot know which values are intentionally public.

## Static and formal tools

ct-verif (Almeida, Barbosa, Barthe, Dupressoir, Emmi, "Verifying Constant-Time Implementations", USENIX Security 2016) verifies constant-time behaviour at LLVM IR level using a product-program construction. Binsec/Rel works on binaries with relational symbolic execution. These tools give guarantees over all paths, but need setup, and the guarantee is relative to their model of leakage (usually branches and addresses, not microarchitectural effects such as speculative execution).

## Limits

- Constant-time at the instruction level does not cover speculative execution leaks, power or electromagnetic side channels.
- Hardware matters: whether multiplication is constant time depends on the CPU.
- Tools check the binary you test. A different compiler version or flag can introduce a leak, so check release builds in CI.

## Worked example

*Execution status: not executed in this book's CI; verify locally. No output shown here was observed by the author.*

Find a secret-dependent branch in a naive tag comparison with Valgrind, on your machine.

1. Install Valgrind and its development headers (`valgrind` and, on some distributions, `valgrind-devel`).
2. Write `cmp.c` with two functions: `naive_eq`, which returns early at the first differing byte, and `ct_eq`, which ORs together XORs of all bytes and returns whether the result is zero.
3. In `main`, fill a 16-byte "expected tag" buffer, mark it with `VALGRIND_MAKE_MEM_UNDEFINED`, and call each function on a received tag. Mark the return value of `ct_eq` as defined before using it, because the comparison result is intentionally public.
4. Compile with `gcc -O2 -g cmp.c -o cmp` and run `valgrind ./cmp`.
5. Expect memcheck to report a conditional jump for `naive_eq`. For `ct_eq`, check whether a report appears; if it does, inspect the assembly with `objdump -d` to see whether the compiler introduced a branch.
6. Repeat at `-O0` and `-O3` and compare.

## Common pitfalls

- Forgetting to declassify public outputs, producing false positives that train people to ignore the tool.
- Running dudect for a few seconds and declaring code constant time.
- Testing a debug build when the release build is compiled differently.
- Assuming that "constant time" also means resistant to speculative execution or power analysis.
- Covering only the paths exercised by one test input with dynamic tools.

## Exercises

1. Complete the worked example and record which optimisation levels produce reports for each function.
2. Run dudect on a naive and a constant-time comparison function for 10 minutes each and compare t-statistics.
3. Pick a small function from a crypto library you use, add Valgrind secret marking to its test, and document every report as a real leak, an intentional declassification, or a false positive.

## Further reading

- Reparaz, Balasch, Verbauwhede, "Dude, is my code constant time?", DATE 2017.
- Almeida et al., "Verifying Constant-Time Implementations", USENIX Security 2016.
- Adam Langley, "Checking that functions are constant time with Valgrind", ImperialViolet blog, 2010.
- [dudect](https://github.com/oreparaz/dudect)
- Jancar et al., "They're not that hard to mitigate: What Cryptographic Library Developers Think About Timing Attacks", IEEE S&P 2022.
