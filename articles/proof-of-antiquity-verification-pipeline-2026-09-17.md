# Proof of Antiquity as a Verification Pipeline: What RustChain Measures—and What It Does Not

**Published:** 2026-09-17 (Asia/Shanghai)  
**Author:** ShaXiaozhu, with AI-assisted research and editing  
**Project examined:** [Scottcjn/Rustchain](https://github.com/Scottcjn/Rustchain)

RustChain’s most interesting idea is easy to state: give real, older hardware a larger share of a fixed reward pool. The harder question is how a network can tell the difference between a physical machine, a virtual machine, an emulator, and a false hardware claim.

The answer in the current protocol is not a single magic test. RustChain uses an **attestation pipeline**. A miner collects several kinds of hardware-derived behavior, submits a structured report, and the node checks whether the signals agree with the claimed machine. If the report passes policy and anti-abuse checks, the machine can participate in the epoch ledger. Its reward share is then influenced by an antiquity multiplier.

That distinction matters: Proof of Antiquity does not directly measure the calendar age of every transistor. It evaluates evidence about hardware identity and behavior, then applies a protocol policy that assigns more weight to selected older or rarer classes of hardware.

## From a machine to an epoch reward

The protocol specification describes five practical stages:

1. The miner obtains the current challenge or epoch context.
2. The miner collects hardware identity fields and behavioral measurements.
3. The miner submits a signed or structured attestation payload.
4. The node validates identity, timing, fingerprint consistency, rate limits, and eligibility.
5. Eligible miners enter an epoch whose fixed reward pool is divided by validated weight.

Conceptually, the settlement rule is:

```text
reward_i = epoch_pot × (weight_i / sum(weight_all_eligible_miners))
```

This separates **participation** from **reward weighting**. The project describes the baseline as “1 CPU = 1 vote”: faster CPUs do not receive extra participation slots merely because they can run more threads. Antiquity then changes the weight used to divide the epoch pot.

The current documentation specifies an epoch as 144 blocks, approximately 24 hours, with a fixed 1.5 RTC epoch pot. Those are protocol parameters, not a promise that any individual miner will earn a particular amount. A miner’s share depends on eligibility, the weight table, and the total eligible weight in that epoch.

## Why RustChain combines several hardware signals

A virtual machine can copy a CPU model string. An emulator can expose an expected architecture name. Neither self-reported field proves that the claimed physical machine exists. RustChain therefore combines six core checks:

- clock drift and oscillator variance;
- cache timing characteristics;
- SIMD identity and timing;
- thermal response;
- instruction-path jitter;
- anti-emulation and virtualization heuristics.

Retro platforms may also use ROM-related evidence.

Each signal addresses a different failure mode. Architecture and SIMD features can expose an impossible combination. Cache measurements can reveal an unexpectedly flat memory hierarchy. Hypervisor, container, DMI, and CPUID artifacts can identify common virtualized environments. Timing and thermal measurements try to capture physical variation that a synthetic environment may reproduce poorly.

The useful idea is **cross-signal consistency**. No individual timing sample should be treated as a unique hardware serial number. Operating-system scheduling, background load, cooling, power management, and measurement noise can all change results. Confidence becomes more meaningful when several independent observations agree over time with the claimed architecture and with previous attestations from the same identity.

## What the evidence can support

A successful attestation can support a bounded claim: the submitted observations were consistent enough with the network’s current validation policy for the miner to be enrolled.

It should not automatically be expanded into stronger claims such as:

- the machine’s manufacturing year was cryptographically proven;
- the fingerprint can never be spoofed;
- every accepted miner is a unique human participant;
- the machine performed useful application work beyond producing the attestation;
- preserving that machine necessarily saved more energy than replacing it.

The protocol documentation itself gives the better framing: hardware signals make spoofing more expensive and brittle. That is a security engineering goal, not an absolute proof theorem.

This is also why public, repeatable evidence matters. RustChain exposes read-only endpoints for node health, the current epoch, active miners, wallet balances, and wallet history. Observers can inspect the network without relying only on screenshots or marketing copy:

```bash
curl -fsS https://rustchain.org/health
curl -fsS https://rustchain.org/epoch
curl -fsS https://rustchain.org/api/miners
curl -fsS "https://rustchain.org/wallet/balance?miner_id=YOUR_MINER_ID"
```

These endpoints show what the running service reports. They do not independently validate every internal measurement, but they provide a concrete surface for monitoring enrollment, recent attestations, epoch parameters, and settlements.

## The preservation incentive is real, but conditional

Proof of Work normally rewards specialized throughput. Proof of Stake rewards capital placed at risk. RustChain instead attempts to reward continued operation of diverse physical computers, with larger multipliers for selected vintage classes.

That can create a preservation incentive. A working PowerPC, SPARC, MIPS, 68K, POWER, or older x86 machine may gain a reason to remain maintained, documented, and connected rather than discarded. The cultural value is clearer than a generic claim that every old computer is environmentally beneficial: keeping rare working machines active preserves knowledge about architectures, toolchains, operating systems, and hardware behavior that otherwise disappears.

The environmental case needs measurement. An old machine can consume more energy per unit of work than a modern one. Reuse is most convincing when it avoids manufacturing a replacement, when the machine performs useful work or preservation research, and when its electricity use remains proportionate. A multiplier alone cannot answer that lifecycle question.

## A practical evaluation checklist

Someone evaluating Proof of Antiquity should ask four groups of questions.

### 1. Measurement quality

- Are timing samples repeated and summarized statistically?
- Are noisy results retried or rejected consistently?
- Are operating-system and architecture differences calibrated?
- Can maintainers reproduce a miner’s classification from retained evidence?

### 2. Adversarial resistance

- Which signals can an attacker directly control?
- Which combinations are expensive to simulate together?
- How are identical or clustered fingerprints detected?
- What happens when a legitimate unusual machine looks like an emulator?

### 3. Economic behavior

- Does one physical machine obtain only one eligible identity?
- Does adding more machines reduce each miner’s share as expected?
- Are multiplier and epoch changes visible before they affect rewards?
- Can settlement records be reconciled with wallet history and external anchors?

### 4. Preservation outcomes

- Which physical machines remain operational because of the incentive?
- Is hardware provenance documented beyond a model label?
- Are power consumption and avoided replacement assumptions recorded?
- Does the network preserve software knowledge as well as hardware?

These questions do not weaken the idea. They turn an attractive narrative into an engineering program that can be tested.

## Why the design is worth watching

RustChain treats the machine itself as part of consensus identity. That is different from storage networks, which prove possession of data, and from compute markets, which pay for completed jobs. The object being made scarce is a continuing, attestable relationship between an identity and a physical computer.

If the attestation pipeline remains transparent, calibrated, and open to adversarial testing, Proof of Antiquity can become more than a multiplier table. It can be a useful experiment in hardware-bound Sybil resistance and in making computer preservation economically visible.

The strongest version of the project’s thesis is therefore modest and testable: **multiple physical signals can make large-scale synthetic hardware identities harder and more expensive, while reward policy can direct value toward machines the network wants to preserve.** Whether that produces durable security and preservation benefits should be judged from measurements, false-positive rates, settlement history, and real machines kept in service.

## Primary sources

- [RustChain protocol specification](https://github.com/Scottcjn/Rustchain/blob/main/docs/PROTOCOL.md)
- [RustChain whitepaper](https://github.com/Scottcjn/Rustchain/blob/main/docs/WHITEPAPER.md)
- [RustChain repository README](https://github.com/Scottcjn/Rustchain/blob/main/README.md)
- [Bounty #99: Challenge Mode](https://github.com/Scottcjn/rustchain-bounties/issues/99)

## Disclosure

AI assistance was used to compare the current project documentation, structure the analysis, and edit the article. The technical claims above were checked against the linked primary project sources on 2026-09-17. No acceptance, token value, or bounty payment is claimed by this publication.