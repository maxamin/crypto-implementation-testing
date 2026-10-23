# Glossary

- **Test vector:** an input with its expected output or expected accept/reject decision.
- **Known-answer test (KAT):** a test comparing output against a published expected value.
- **Wycheproof:** collection of attack-oriented test vectors for cryptographic libraries.
- **Differential fuzzing:** feeding the same inputs to several implementations and flagging disagreements.
- **Constant-time code:** code whose branches, memory addresses and instruction timings do not depend on secrets.
- **Declassification:** intentionally treating a secret-derived value as public, such as a final signature.
- **Nonce reuse:** using the same per-signature or per-encryption nonce twice; catastrophic for ECDSA and AES-GCM.
- **Signature malleability:** the ability to transform a valid signature into a different valid signature for the same message.
- **Low-s rule:** requiring ECDSA `s` to be at most half the curve order, giving a canonical signature.
- **Invalid curve attack:** supplying a point not on the intended curve to extract information about a private key.
- **Small-order point:** a point whose multiples cycle quickly; can leak key bits or bypass checks if not rejected.
- **Functional correctness proof:** a machine-checked proof that code computes its specification for all inputs.
- **Trusted computing base (TCB):** components (specs, provers, compilers) that a verification result relies on.
