# Resources

## Test vectors
- [Project Wycheproof](https://github.com/C2SP/wycheproof): attack-oriented test vectors for many algorithms.
- [NIST CAVP](https://csrc.nist.gov/projects/cryptographic-algorithm-validation-program): known-answer test vectors for approved algorithms.

## Fuzzing and differential testing
- [Cryptofuzz](https://github.com/MozillaSecurity/cryptofuzz): differential fuzzer for cryptographic libraries.
- [OSS-Fuzz](https://google.github.io/oss-fuzz/) and [cargo-fuzz](https://rust-fuzz.github.io/book/cargo-fuzz.html)

## Constant-time tools
- [dudect](https://github.com/oreparaz/dudect): statistical timing leakage test.
- [Valgrind](https://valgrind.org/): memcheck client requests for secret-tainting (the ctgrind technique).
- TIMECOP, part of the [SUPERCOP](https://bench.cr.yp.to/supercop.html) benchmarking framework.

## Formal methods
- [HACL*](https://github.com/hacl-star/hacl-star), [fiat-crypto](https://github.com/mit-plv/fiat-crypto)
- [SAW](https://saw.galois.com/) and [Cryptol](https://cryptol.net/)
- [Jasmin](https://github.com/jasmin-lang/jasmin)

## Papers and posts
- Reparaz, Balasch, Verbauwhede, "Dude, is my code constant time?", DATE 2017.
- Almeida et al., "Verifying Constant-Time Implementations", USENIX Security 2016.
- Zinzindohoué et al., "HACL*: A Verified Modern Cryptographic Library", ACM CCS 2017.
- Erbsen et al., "Simple High-Level Code For Cryptographic Arithmetic – With Proofs, Without Compromises", IEEE S&P 2019.
- Barbosa et al., "SoK: Computer-Aided Cryptography", IEEE S&P 2021.
- Jager, Schwenk, Somorovsky, "Practical Invalid Curve Attacks on TLS-ECDH", ESORICS 2015.
- Chalkias, Garillot, Nikolaenko, "Taming the Many EdDSAs", SSR 2020.
- Breitner, Heninger, "Biased Nonce Sense", Financial Cryptography 2019.
- Neil Madden, "Psychic Signatures in Java", 2022.

## Standards
- [RFC 6979](https://www.rfc-editor.org/rfc/rfc6979), [RFC 8032 (EdDSA)](https://www.rfc-editor.org/rfc/rfc8032), [EIP-712](https://eips.ethereum.org/EIPS/eip-712)

## Related books in this wiki
- **mpc-wallet-security**, **zk-circuit-security**, **ai-agent-wallet-security**, **compiler-fuzzing**, **lean4-security-proofs**
