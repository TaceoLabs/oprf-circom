## OPRF Circom Circuits

[![Circom Tests](https://github.com/TaceoLabs/oprf-circom/actions/workflows/circom_test.yml/badge.svg)](https://github.com/TaceoLabs/oprf-circom/actions/workflows/circom_test.yml)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Circom circuits and a Mocha/chai test harness for the TwoHashDH-based verifiable OPRF used by the TACEO OPRF service. This repository was extracted from the [`TaceoLabs/oprf-service`](https://github.com/TaceoLabs/oprf-service) monorepo (the `circom` directory plus its test suite) so the circuits can evolve independently of the Rust services that consume them.

For a full description of the scheme these circuits implement, see [`docs/oprf.pdf`](https://github.com/TaceoLabs/oprf-service/blob/main/docs/oprf.pdf) in the upstream monorepo.

## Layout

```
circuits/
  main/                       top-level circuits with `component main`
    OPRFQueryProof.circom       client-side OPRF query proof
    OPRFNullifierProof.circom   client-side nullifier proof
    OPRFKeyGenProof{13,25,37}.circom   DKG proofs for t-of-n thresholds
  client_side_proofs/         composition templates for the main circuits
  oprf_keys/                  OPRF key-generation templates
  babyjubjub/                 Baby Jubjub group operations
  poseidon2/                  Poseidon2 permutation (t = 2, 3, 4, 8, 12, 16)
  eddsa_poseidon2/            EdDSA signature verification over Poseidon2
  merkle_tree/                binary Merkle tree root check
  encode_to_curve_babyjj/     hash-to-curve (Elligator2) for Baby Jubjub
  verify_dlog/, sgn0/, quadratic_residue/, inverse_or_zero/   helpers
  circomlib/                  vendored subset of iden3/circomlib

tests/                        Mocha test suite (see `tests/README.md`)
```

Top-level circuits in `circuits/main/`:

* `OPRFQueryProof` — proves a client's OPRF query is well-formed against a credential committed in a Merkle tree.
* `OPRFNullifierProof` — proves correctness of a nullifier derived from an OPRF evaluation for a given relying party / action.
* `OPRFKeyGenProof{13,25,37}` — DKG correctness proofs parameterized for `1-of-3`, `2-of-5`, and `3-of-7` threshold setups.

## Prerequisites

* Node.js 18+ (CI uses Node 24)
* Circom v2.2.x on `PATH` (CI pins [`v2.2.3`](https://github.com/iden3/circom/releases/tag/v2.2.3))
  * Check: `circom --version`
  * Install: <https://docs.circom.io/getting-started/installation/>

## Running the tests

```bash
cd tests
npm ci
npm test
```

Mocha discovers every `tests/tests/**/*.test.js`. Each test compiles the target circuit via [`circom_tester`](https://www.npmjs.com/package/circom_tester), generates a witness, and checks the constraints; a few tests also use [`snarkjs`](https://www.npmjs.com/package/snarkjs) to exercise proving/verification end-to-end.

## License

This project is licensed under the [MIT License](LICENSE).

The vendored [iden3/circomlib](https://github.com/iden3/circomlib) files under `circuits/circomlib/` are licensed under the [GNU GPL v3](circuits/circomlib/LICENSE) and retain their original terms.
