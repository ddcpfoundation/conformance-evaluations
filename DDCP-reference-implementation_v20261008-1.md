# Evaluation: DDCP reference implementation

DDCP conformance evaluation, v20261008-1. Published by DDCP Foundation Inc under the [evaluation methodology](https://github.com/ddcpfoundation/protocol-governance/blob/main/EVALUATION_METHODOLOGY.md), against the [DDCP conformance criteria](https://github.com/ddcpfoundation/protocol-governance/blob/main/CRITERIA.md). Licensed under CC BY 4.0.

File names cited below (SPECIFICATION, LIMITS.md, BUILD_VERIFICATION.md, UPGRADE_PROCEDURE.md) are files of the reference repository at the examined commit.

## Object

| | |
|---|---|
| Examined | The DDCP reference implementation, as code, at commit [TO CONFIRM: commit of the published snapshot] of `ddcpfoundation/[TO CONFIRM: reference repository name]`, as it would run in a deployment that carries value |
| Chain | Solana, on the Token-2022 program |
| Not examined | Any currency. No currency has been issued with this code. |
| Evidence of behavior | The Foundation's devnet demonstration: program `Bn36ThBHETRi1qBGSauPmocKRFfzFGvdvnAn7SAb1Jp`, reference mint `9RTSRMFRCLKHLEzyKcTEypz5R45tPUctNMLir98y1iRa` |
| On-chain reading | [TO MEASURE: date (UTC) and slot of the reading] |
| Build verification | The deployed demonstration program's executable hash [TO MEASURE: hash read on chain] equals the hash of the examined commit built in the verifiable-build image named in BUILD_VERIFICATION.md |
| Relationship | The reference implementation is the Foundation's own work. The Foundation examines it here; this evaluation is not independent. No issuer is involved. |

Where a criterion depends on choices the code leaves to whoever creates a mint, the result records what the code fixes and what it leaves open, including any choice it permits that would defeat the criterion (methodology, section 1).

## Summary

| # | Criterion | Result |
|---|---|---|
| 1 | Control | Delivered |
| 2 | Unconditionality | Delivered |
| 3 | Settlement | Not delivered |
| 4 | Privacy: balance and amount | Partly delivered |
| 5 | Privacy: sender and receiver | Not delivered |
| 6 | Privacy: timing and frequency | Not delivered |
| 7 | Lawful access | Partly delivered |
| 8 | Backing | Partly delivered |
| 9 | Reserve structure | Partly delivered |
| 10 | Reserve dispersion | Not delivered |
| 11 | Fee ceilings | Partly delivered |
| 12 | Authority allocation | Partly delivered |
| 13 | Accurate disclosure of capabilities | Delivered |
| 14 | Disclosure of mutability | Delivered |
| 15 | Basics and payments | Partly delivered |

The summary is an index to the results below, not a score.

## Results

### 1. Control: Delivered

**What the code fixes.**
- The genesis instruction creates every mint with no Freeze Authority, no Permanent Delegate and a Confidential Transfer mint authority of none, and refuses any other Confidential Transfer mint authority.
- Token-2022 accepts neither a Freeze Authority nor a Permanent Delegate after a mint is initialized, so the program's upgrade authority cannot add them to an existing mint.
- Holders' transfers are processed by Token-2022, not by this program.

**What bounds it.** The absences hold under the current Token-2022 code. Whoever holds Token-2022's upgrade key could change that code, for every token at once: [TO MEASURE: holder of the Token-2022 program's upgrade authority].

**Evidence.**
- Source: genesis checks and fixed configuration (SPECIFICATION section 2.1; I-1 checks, error 6011).
- On chain: the reference mint carries no Freeze Authority and no Permanent Delegate extension, and its Confidential Transfer mint authority is none.

### 2. Unconditionality: Delivered

**What the code fixes.** No spending restriction, expiry or behavioral condition exists in the program or can be set on a mint it creates. The genesis fixes exactly five extensions, none of them a Transfer Hook, and no further extension can be added except Token Metadata, which carries none of these. No program other than Token-2022 executes on a transfer.

**What bounds it.** As 1.

**Evidence.**
- Source: SPECIFICATION section 2.1.
- On chain: the reference mint's extensions are exactly Confidential Transfers, Transfer Fee, Confidential Transfer Fee, Metadata Pointer and Token Metadata.

### 3. Settlement: Not delivered

Transfers settle by the consensus of the chain the code runs on. Nothing in the code places settlement beyond the decision of a single government, and the Foundation does not claim the aim is realized.

### 4. Privacy: balance and amount: Partly delivered

**What the code delivers.** A holder who turns on Confidential Balances has an encrypted balance and encrypted transfer amounts. The tool turns it on only after asking.

**What it leaves open or does not deliver.**
- It covers only holders who turn it on, and public balances and their history are visible.
- Amounts moved into and out of the confidential balance are visible.
- When a mint charges a fee, the withheld-fee decryption key narrows the amount of each confidential transfer to a band of 10,000 divided by the rate, in base units. The code permits a rate ceiling up to 10,000 basis points, at which this band narrows to a single base unit. The genesis sets the fee schedule to zero; any later rate is the mint's.

**Evidence.**
- Source: SPECIFICATION sections 7 and 12.
- On chain: the reference mint's fee schedule is [TO MEASURE: rate and maximum fee in force at the reading].

### 5. Privacy: sender and receiver: Not delivered

The sending and receiving token accounts of every transfer are visible on chain. Token-2022's confidential transfers conceal amounts and balances only.

### 6. Privacy: timing and frequency: Not delivered

Every transaction is timestamped and attributable to its accounts on chain.

### 7. Lawful access: Partly delivered

**What the code delivers.** There is no administrative override: no key reaches value held in self-custody (see 1). The code identifies no one and records nothing about anyone.

**What it leaves open.** The intermediary layer is outside the code. Whether identity and records are reachable through judicial process, and only through it, depends on the intermediaries each currency uses.

### 8. Backing: Partly delivered

**What the code delivers.**
- Supply is public on the mint.
- Every mint needs the reserve key's signature beside the issuer's.
- The reserve key publishes a reserve statement: a figure in the currency's unit of account, the time of publication and a link to a document, which the specification asks to be dated and hash-pinned.

**What it leaves open.**
- The program does not check the statement, and minting does not depend on it. Whether a currency is fully backed, and whether its statement is verifiable, depend on that currency's reserves, documents and attestor.
- The program does not enforce redemption.

**Evidence.** SPECIFICATION sections 9 and 10; on chain, the reference mint's reserve statement reads 0 with a hash-pinned link, consistent with a test instrument that holds no reserve.

### 9. Reserve structure: Partly delivered

**What the code delivers.** The reserve role is a distinct co-signer key, required for minting and burning and alone able to publish the reserve statement. The three co-signer keys must stay distinct.

**What it leaves open.**
- Distinct keys are not independent holders: the code permits one holder for all three.
- Separation of reserve management into an independent foundation, ring-fencing and independent attestation are not constrained by the code.

### 10. Reserve dispersion: Not delivered

The code places no constraint on where reserves are held. Dispersion, or the disclosure of the regulatory constraint preventing it, is each currency's.

### 11. Fee ceilings: Partly delivered

**What the code delivers.** A rate ceiling and an absolute per-transfer ceiling are set at genesis, stored by the program and never raised. Fee changes above either are refused.

**What it leaves open, including choices that defeat the criterion.**
- The ceiling values are the mint creator's. The code permits a rate ceiling of 10,000 basis points, a fee of up to the whole transfer, capped only by the absolute ceiling the creator also chooses.
- The ceilings are enforced by the program, so whoever holds the deployed program's upgrade authority can remove the check. The upgrade arrangement is the deploying currency's choice; the reference describes a time-locked multisig or no authority after an audit, and enforces neither.

**Evidence.** SPECIFICATION sections 2.1 and 4 (I-6, errors 6008 and 6009); UPGRADE_PROCEDURE.md.

### 12. Authority allocation: Partly delivered

**What the code delivers.**
- Minting needs the issuer and the reserve; fee changes need the issuer and the operator.
- A co-signer key is replaced only by the operator and the reserve, so the issuer can be replaced without its own signature.
- The co-signer keys are distinct at genesis and at every replacement.
- The specification states the replacement rule.

**What it leaves open, including choices that defeat the criterion.**
- The code permits a single holder for every co-signer key and every other authority, so a single agent could hold authorities that together allow halting or dilution. The devnet demonstration is held that way, and carries no value.
- The program's upgrade authority, which contains every other control, is whatever the deploying currency sets.
- The metadata and withheld-fee authorities go to whatever keys the mint creator names; the code provides no multi-party control.

### 13. Accurate disclosure of capabilities: Delivered

The specification (docs/SPECIFICATION.md) states every capability the program creates over a mint, who can exercise it and under what conditions, and was examined against the source at the examined commit. `ddc state` reads every authority except the withheld-fee decryption key from the chain.

The code has not had an independent audit. That bears on how far the code can be relied on, not on whether its capabilities are accurately stated, and is recorded in the reference implementation's Known Limits against DDCP.

### 14. Disclosure of mutability: Delivered

The specification, section 2, states what the genesis fixes for every mint, what it leaves to the mint creator and what can be changed afterward, and by whom.

### 15. Basics and payments: Partly delivered

**What the code delivers.** The currency is divisible to six decimals, transferable at any hour, fungible and counterfeit-proof to the extent the chain is. Public and confidential transfers each take one transaction.

**What it does not deliver.**
- Holders need SOL for network fees and account rent.
- A confidential balance is readable only by wallet software that derives its keys the same way. Wallet compatibility has not been measured [TO CONFIRM: results, if measured before publication].

## Comparison with the reference implementation's Known Limits against DDCP

The reference implementation publishes its own Known Limits against DDCP (LIMITS.md at the examined commit). This evaluation found no limit that LIMITS.md does not disclose, and LIMITS.md claims none this evaluation does not find.

It differs in one point. LIMITS.md lists the absence of an independent audit under criterion 13. This evaluation records criterion 13 as delivered, because the criterion concerns the accuracy of the specification, not its assurance.

## When this evaluation becomes stale

It is stale, and the registry will mark it so, when any of the following occurs:

- the reference implementation publishes a commit that changes the program, the genesis or the specification;
- the devnet demonstration's program is upgraded or its upgrade authority changes;
- Token-2022 changes in a way the reference's Known Limits against DDCP identifies as bounding a criterion;
- the conformance criteria or the methodology change in a way that affects a result above.
