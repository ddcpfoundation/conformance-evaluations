# DDCP conformance evaluation registry

This repository is the DDCP conformance evaluation registry: the evaluations DDCP Foundation Inc has published of currencies, and of its own reference implementation, against the [DDCP conformance criteria](https://github.com/ddcpfoundation/protocol-governance/blob/main/CRITERIA.md).

## What an evaluation is

An evaluation examines one currency, on one chain, at one point in time, against every criterion. For each criterion it records one of three results, with the evidence behind it: delivered, partly delivered, or not delivered. It is a per-criterion record, not a verdict: no score, grade or badge is derived from it.

How evaluations are made, what evidence they rest on and when they become stale is set out in the [evaluation methodology](https://github.com/ddcpfoundation/protocol-governance/blob/main/EVALUATION_METHODOLOGY.md).

- The registry records the Foundation's own evaluations only. An issuer's self-evaluation is input to the Foundation's examination, never an entry here.
- Each evaluation states any relationship between the Foundation, its directors, officers or funders and the issuer examined.
- The Foundation does not describe a currency as meeting any criterion without having examined it.
- The absence of an evaluation is neither an endorsement nor a judgment.

## Evaluations

| Evaluation | Object | On-chain reading | Status |
|---|---|---|---|
| [DDCP reference implementation, v20261008-2](DDCP-reference-implementation_v20261008-2.md) | The DDCP reference implementation on Solana's Token-2022 program, as code | 2026-10-08 | Current |

An evaluation is never rewritten. A new evaluation of the same object is a new file; a stale one stays here, marked with the date and the reason; an error is corrected by a dated addendum to the same file.

## Reporting an error

Report an error in an evaluation as an issue in this repository, citing the evaluation, the criterion and the evidence. The issuer of the currency examined is notified of any correction.

## License

The evaluations are published by DDCP Foundation Inc under the [Creative Commons Attribution 4.0 International license](LICENSE) (CC BY 4.0). DDCP is a trademark of DDCP Foundation Inc (U.S. application pending); the license covers the evaluations, not the mark.
