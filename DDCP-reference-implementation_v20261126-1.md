# DDCP Evaluation: DDCP reference implementation

DDCP Evaluation, v20261126-1. Published by DDCP Foundation Inc under the [DDCP Evaluation Methodology](https://github.com/ddcpfoundation/protocol-governance/blob/criteria-v20261126-1/EVALUATION_METHODOLOGY.md), against the [DDCP Evaluation Criteria](https://github.com/ddcpfoundation/protocol-governance/blob/criteria-v20261126-1/CRITERIA.md), criteria-v20261126-1. Licensed under CC BY 4.0.

File names cited below (LIMITS.md, docs/SPECIFICATION.md, BUILD_VERIFICATION.md, docs/UPGRADE_PROCEDURE.md, README.md) are files of the reference repository at commit `9cbcec613cf67a5660c021770503beead6d87d12`.

## Object

| | |
|---|---|
| Examined | The DDCP reference implementation, as code, at commit `375c466cb7781131ee263160a1b4cb76bf72ceca`, tagged `v0.3.0`, of [`ddcpfoundation/ddcp-ddc`](https://github.com/ddcpfoundation/ddcp-ddc), as it would run in a deployment that carries value. Its documentation is read at commit `9cbcec61`, in which `program/` and `cli/` are identical to `v0.3.0`. |
| Chain | Solana, on the Token-2022 program |
| Not examined | Any currency. No currency has been issued with this code. |
| Evidence of behavior | The Foundation's devnet demonstration: program `Bn36ThBHETRi1qBGSauPmocKRFfzFGvdvnAn7SAb1Jp`, reference mint `9RTSRMFRCLKHLEzyKcTEypz5R45tPUctNMLir98y1iRa`. |
| On-chain reading | 2026-11-26, from 08:51 UTC; devnet slots 526,454,673 to 526,455,249 (epoch 1218); mainnet-beta slots 474,087,751 to 474,090,661, the last recorded (epoch 1097) |
| Build verification | The deployed demonstration program's executable hash, `df375a9119e35c7badffe780e44f2175d57199dbfc07aa89aa2fd0e5ba7ffbd0`, equals the hash of `v0.3.0` built as BUILD_VERIFICATION.md describes; the program was last deployed at devnet slot 523,214,795. Its upgrade authority is `8J6DmzQ8ZFLpAgnELpqtRmbL9ve6AB3nmGhxAG7bcWvR`, which README.md calls the deploy key. |
| Relationship | The reference implementation is the Foundation's own work. The Foundation examines it here; this evaluation is not independent. No issuer is involved. |

This evaluation replaces evaluation v20261011-2, made under criteria-v20261008-2, which stays in the registry marked stale. The criteria and the methodology changed between the two, including a fourth result for code, so the results are not comparable criterion by criterion; the change and the old-to-new numbering are in [the published rationale](https://github.com/ddcpfoundation/protocol-governance/blob/criteria-v20261126-1/rationale/criteria-v20261126-1.md).

"Not deliverable by code alone" means that the code enables the criterion or partly delivers it, and that whether it is delivered depends on choices each currency makes, at its creation or outside the code. For every criterion that depends on such choices, whatever its result, the record lists the choices the code permits that would defeat it (methodology, section 1).

## Summary

| # | Criterion | Result |
|---|---|---|
| 1 | Basics and payments | Delivered |
| 2 | Control | Delivered |
| 3 | Unconditionality | Delivered |
| 4 | Settlement | Not delivered |
| 5 | Privacy: balance and amount | Delivered |
| 6 | Privacy: parties and timing | Not delivered |
| 7 | Fee ceilings | Not deliverable by code alone |
| 8 | Authority allocation | Not deliverable by code alone |
| 9 | Lawful access only through judicial process | Not deliverable by code alone |
| 10 | Backing | Not deliverable by code alone |
| 11 | Reserve structure | Not deliverable by code alone |
| 12 | Reserve dispersion | Not deliverable by code alone |
| 13 | Accurate disclosure of security and capabilities | Delivered |
| 14 | Disclosure of mutability | Delivered |

The summary is an index to the results below, not a score.

## Results

### 1. Basics and payments: Delivered

**What the code delivers.** The currency is divisible to six decimals, fungible, transferable to any Solana address at any hour, and counterfeit-proof to the extent the chain is. A public transfer and a confidential transfer each take one transaction.

**How the criterion is read.** "Low-cost" is read as applying to a transfer itself. A transfer fee a currency charges on top of it is examined under criterion 7, where the code's ceilings are recorded together with the choices that would defeat them: the code permits a rate ceiling of up to 10,000 basis points, a fee of up to the whole transfer.

**Facts recorded.**
- Holders need SOL for network fees and account rent (LIMITS.md, section 1).
- A confidential transfer is one transaction for the sender. The recipient's account must have Confidential Balances turned on, and the recipient runs `apply-pending`, a transaction of its own, before the amount can be spent (LIMITS.md, section 1).

**Evidence.**
- On chain, at the reading: the reference mint has six decimals.
- Six confidential transfers of the reference mint were found, the latest on 2026-11-18; each is one transaction with one signature, carrying the transfer and its five proof verifications.

### 2. Control: Delivered

**What the code fixes.**
- The genesis instruction creates every mint with no Freeze Authority, no Permanent Delegate and a Confidential Transfer mint authority of none, and refuses any other Confidential Transfer mint authority.
- Token-2022 accepts neither a Freeze Authority nor a Permanent Delegate after a mint is initialized, so no upgrade of the deployed program can add them to an existing mint.
- Holders' transfers are processed by Token-2022, not by this program.

**What bounds it.** The absences hold under the current code of two programs this implementation depends on and does not control.

- **Token-2022.** Whoever holds its upgrade authority could change its code, for every token at once, including how existing token accounts behave.
  - Mainnet-beta: the upgrade authority `AeLmXCbPaQHGWRLr2saFsEVfmMNuKnxRAbWCT9P5twgz` is the signing address of the multisig account `BnD53519LK7QM4ERBGvLedZDe1VoTPgtiBkGPWAACiMZ` of the Squads multisig program (v3), which requires 4 of its 6 member keys. The v3 multisig record has no time-lock field. Who holds the member keys was not examined.
  - Devnet: the upgrade authority `3URRPr96EV2wuNRgQKwQpuZitHHsVyDUen1eRSvEun9G` is a single key, which is also one of the six members of the mainnet-beta multisig.
  - Upgrades since 2026-06-17: on mainnet-beta, two (2026-06-17 and 2026-10-23), each through the multisig; on devnet, one (2026-09-23). At the reading, both clusters run the same Token-2022 code.
- **The ZK ElGamal Proof program**, which verifies the proofs behind confidential balances. It is changed by validator software and feature activation, not by Token-2022's upgrade authority. It was patched for one security flaw in April 2025 and disabled for another in June 2025, with Token-2022's confidential transfer instructions disabled with it. The Solana Foundation's post-mortems of 2025-05-02 and 2025-06-25 describe each flaw as one that would have let a forged proof pass verification; no exploit is known. At the reading it was executable on both clusters, and the features that enable it were active.

**Evidence.**
- Source: docs/SPECIFICATION.md, sections 2.1 and 3.1; genesis checks, error 6011.
- On chain, at the reading: the reference mint has no Freeze Authority, no Permanent Delegate extension and a Confidential Transfer mint authority of none, so automatic approval and the absence of an auditor key are permanent.

### 3. Unconditionality: Delivered

**What the code fixes.** No spending restriction, expiry or behavioral condition exists in the program or can be set on a mint it creates. The genesis fixes exactly five extensions, none of them a Transfer Hook; no other extension can be added afterward except Token Metadata, which the genesis already initializes and which carries none of these. No program of the currency's runs on a transfer: a public transfer is processed by Token-2022 alone, and a confidential transfer by Token-2022, with the ZK ElGamal Proof program verifying its proofs.

**What bounds it.** As 2.

**Evidence.**
- Source: docs/SPECIFICATION.md, section 2.1.
- On chain, at the reading: the reference mint's extensions are exactly Transfer Fee, Confidential Transfers, Confidential Transfer Fee, Metadata Pointer and Token Metadata; Mint Close Authority, Default Account State, Permanent Delegate, Transfer Hook and Pausable are absent.

### 4. Settlement: Not delivered

Transfers settle by the consensus of the chain the code runs on; nothing in the code bears on it.

At the reading, mainnet-beta ran the Alpenglow consensus rules (SIMD-0326). Under them, the chain stops finalizing if more than 40 percent of stake stops voting, and its guarantee that a finalized transfer stays final holds only while no more than 20 percent of stake acts against the protocol.

Measured by the hosting country of each validator, at epoch 1097:

- no single country hosts validators holding more than 40 percent of stake;
- one country hosts validators holding more than 20 percent of stake;
- countries under one supranational legal order, whose law binds validators directly in each of them, together host validators holding more than 60 percent of stake.

Either of the last two is enough for the criterion not to be met.

**Method and limits of the measurement.** Activated stake per vote account, joined to each validator's network address, located by IP geolocation from one source; 0.02 percent of stake could not be located. The hosting country is where the servers are, not where each operator is legally reachable. One epoch was read.

### 5. Privacy: balance and amount: Delivered

**What the code delivers.** A holder who turns on Confidential Balances has an encrypted balance, and every confidential transfer conceals its amount. The capability is available to every holder of every mint the code creates; the tool turns it on only after asking.

**How the criterion is read.** The criterion is read as applying to the balance and to transfers between holders.

**Facts recorded.**
- Confidential Balances is opt-in: public balances and their history are visible.
- Amounts moved between a holder's public and confidential balances are visible.
- Where a currency charges a transfer fee, the withheld-fee decryption key narrows the amount of each confidential transfer whose fee is below the absolute ceiling to a band of 10,000 divided by the rate, in base units, recorded as visibility (methodology, section 7). The code permits a rate ceiling of up to 10,000 basis points, at which the band narrows to a single base unit. The genesis sets the fee schedule to zero; any later rate is the currency's.
- At the reading, no self-custody wallet application was found that sets up, shows, sends or decrypts confidential balances (desk check of published sources, 2026-11-26). A wallet without that support shows only the public balance.
- No run of a third-party wallet against an account whose keys follow the standard derivation (docs/SPECIFICATION.md, section 12) is recorded. The tool's derivation is tested to match the Token-2022 client library byte for byte. The wallet check recorded in LIMITS.md, section 5, was made on an account set up under an earlier derivation.

**Evidence.**
- Source: docs/SPECIFICATION.md, sections 7 and 12; LIMITS.md, section 5.
- On chain, at the reading: the reference mint's fee in force was 0 basis points with a maximum of 0. The fee history held on the mint shows 100 basis points with a maximum of 1,000,000 base units from devnet epoch 1172, and 0 from epoch 1174.

### 6. Privacy: parties and timing: Not delivered

The sending and receiving token accounts of every transfer, confidential or public, are visible on chain, and every transaction is timestamped and attributable to its accounts. Token-2022's confidential transfers conceal amounts and balances only.

### 7. Fee ceilings: Not deliverable by code alone

**What the code delivers.** A rate ceiling and an absolute per-transfer ceiling are set at genesis, stored by the program and never raised. Fee changes above either are refused. A fee change takes effect two epochs after it is set.

**What it leaves open, including choices that defeat the criterion.**
- The ceiling values are the mint creator's. The code permits a rate ceiling of 10,000 basis points, a fee of up to the whole transfer, capped only by the absolute ceiling the creator also chooses.
- The ceilings are enforced by the program, so whoever holds the deployed program's upgrade authority can remove the check. The upgrade arrangement is each currency's choice; docs/UPGRADE_PROCEDURE.md describes a time-locked multisig or no authority after an audit, and the code enforces neither.

**Evidence.**
- Source: docs/SPECIFICATION.md, sections 2.1 and 4 (I-6, errors 6008 and 6009); docs/UPGRADE_PROCEDURE.md.
- On chain, at the reading: the reference mint's stored ceilings are 100 basis points and 1,000,000 base units. The demonstration program's upgrade authority is a single key.

### 8. Authority allocation: Not deliverable by code alone

**What the code delivers.**
- Minting needs the issuer and the reserve; fee changes need the issuer and the operator.
- A co-signer key is replaced only by the operator and the reserve together, and only with the signature of the key being installed; the issuer can be replaced without its own signature.
- The three co-signer keys are distinct at genesis and at every replacement.
- The specification states the replacement rule (section 3.3).

**What it leaves open, including choices that defeat the criterion.**
- The code requires distinct keys, not independent holders. It permits a single holder for every co-signer key and every other authority, so a single agent could hold authorities that together allow halting or dilution.
- The program's upgrade authority, which contains every other control, is whatever each currency sets.
- The metadata authorities and the withheld-fee authorities go to whatever keys the mint creator names; the code provides no multi-party control of its own over them. Which kind of multi-party arrangement each accepts is in docs/SPECIFICATION.md, section 3.1.

**Evidence.**
- On chain, at the reading: three distinct co-signer keys in the program's record; the Mint Authority and the transfer fee configuration authority are program addresses; the withdraw-withheld, Confidential Transfer fee, Metadata Pointer and Token Metadata update authorities are four distinct single keys, none of them a co-signer key or the upgrade key.
- The demonstration is held by a single keyholder (README.md) and carries no value. Who holds a key cannot be read from the chain.

### 9. Lawful access only through judicial process: Not deliverable by code alone

**What the code delivers.** No administrative override: no key reaches value held in self-custody (see 2). The code identifies no one and records nothing about anyone.

**What it leaves open.** The intermediary layer is outside the code. Whether identity and records are reachable only through judicial process directed at an identified person depends on the intermediaries each currency uses where people enter and leave the system.

### 10. Backing: Not deliverable by code alone

**What the code delivers.**
- Supply is public on the mint.
- Minting always needs the reserve key's signature beside the issuer's.
- The reserve key publishes a reserve statement: a figure in the currency's unit of account, the time of publication and a link to a document whose SHA-256 the link carries.

**What it leaves open.**
- The program does not check the statement, and minting does not depend on it. Whether a currency is fully backed, and whether its statement is verifiable, depend on that currency's reserves, documents and attestor.
- The program does not enforce redemption.

**Evidence.**
- Source: docs/SPECIFICATION.md, sections 9 and 10.
- On chain, at the reading: the reference mint's reserve statement reads 0, published by the reserve key on 2026-10-05. The linked document was fetched and its SHA-256 equals the hash in the link; it states a reserve of 0 and calls itself a self-published reserve statement, consistent with a test instrument that holds no reserve.

### 11. Reserve structure: Not deliverable by code alone

**What the code delivers.** The reserve role is a distinct co-signer key, required for minting and burning and alone able to publish the reserve statement.

**What it leaves open.**
- Distinct keys are not independent holders: the code permits one holder for all three (see 8).
- Separation of reserve management into an independent foundation, ring-fencing per currency and independent attestation are each currency's.

### 12. Reserve dispersion: Not deliverable by code alone

The code places no constraint on where reserves are held and does not prevent dispersion. Dispersion, or the disclosure in a currency's specification of the regulatory constraint preventing it, is each currency's.

### 13. Accurate disclosure of security and capabilities: Delivered

docs/SPECIFICATION.md states:
- every capability the program creates over a mint, who can exercise it and under what conditions (sections 2 and 3), examined against the source at `v0.3.0` and, for Token-2022's own behavior, against Token-2022's published source at commit `2243ffc7` of `solana-program/token-2022`, which was not matched to the build deployed on either cluster;
- the cryptographic and security assumptions the code relies on, including what a quantum computer would break, for control and for privacy (section 15.1 and 15.2);
- that the code has not had an independent security audit (section 15.4);
- what holders are exposed to if an assumption fails, including encrypted history already recorded on chain (section 15.2 and 15.5).

`ddc state` reads every authority, and the public half of the withheld-fee decryption key, from the chain.

The absence of an audit bears on how far the code can be relied on, not on whether the specification states it; it is stated.

**Evidence.** docs/SPECIFICATION.md at `9cbcec61`, SHA-256 `3ba15e412750bd706768add1d0977991aee676d57bf0e6761cd2cd633bb21b4f`.

### 14. Disclosure of mutability: Delivered

docs/SPECIFICATION.md, section 2, states what the genesis fixes for every mint, what it leaves to the mint creator and what can be changed afterward, and by whom.

## Comparison with the reference implementation's Known Limits against DDCP

The reference implementation publishes its own Known Limits against DDCP (LIMITS.md at commit `9cbcec61`). LIMITS.md claims no limit this evaluation does not find. This evaluation differs from it in two points:

- **Settlement.** LIMITS.md records that the code delivers nothing toward criterion 4 and leaves the extent any one country can reach to this evaluation's measurement, which is recorded under 4.
- **Test fixtures.** LIMITS.md, section 13, states that the program's tests run against the Token-2022 builds deployed on mainnet-beta and on devnet. The mainnet-beta fixture was taken on 2026-10-08, before Token-2022's upgrade there on 2026-10-23. At the reading, mainnet-beta runs the same Token-2022 code as devnet, whose fixture is current, so the code tested is the code now deployed on both clusters; the file recorded as the mainnet-beta build is out of date.

## Not examined

- Who holds the co-signer keys, the four other mint authorities and the six members of Token-2022's mainnet-beta multisig. The chain shows keys, not holders.
- Where validator operators are legally reachable, as distinct from where their servers are hosted; stake over more than one epoch; a second geolocation source.
- Third-party wallet support by a run, as distinct from published sources.

## When this evaluation becomes stale

It is stale, and the registry will mark it so, when any of the following occurs:

- the reference implementation publishes a commit that changes the program, the genesis or the specification;
- the devnet demonstration's program is upgraded or its upgrade authority changes;
- Token-2022 or the ZK ElGamal Proof program changes in a way the reference's Known Limits against DDCP identifies as bounding a criterion;
- the chain's consensus rules change;
- the DDCP Evaluation Criteria or the methodology change in a way that affects a result above.
