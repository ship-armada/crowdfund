# Armada Crowdfunding Spec

## Overview

Armada will raise funds by "word-of-mouth whitelisting." The allocation mechanism is algorithmic and predeclared. Once the commitment window opens, participant allocations are determined entirely by the rules of the contract, not by launch team discretion. The initial network shape (who is invited as a seed, and who the launch team invites directly) is determined by the launch team before and during the window. This is intentional and disclosed.

### Parameters

| Parameter | Value |
|---|---|
| Purpose | Fund MVP |
| Minimum fund | $1,000,000 USDC |
| Base fund | 1,200,000 ARM ($1.2M) |
| Max fund (elastic) | 1,800,000 ARM ($1.8M) |
| Expansion trigger | $1,500,000 in capped demand |
| Percent of supply | 10 - 15% |
| Price | $1.00 per ARM |
| Timing | Pre-product |

---

## Token Distribution

**Total supply: 12,000,000 ARM**

| Allocation | ARM | % | Unlock | Voting Power |
|---|---|---|---|---|
| Crowdfund | 1,200,000 - 1,800,000 | 10-15% | Governance proposal | Active once claimed and delegated |
| Team | 1,800,000 | 15% | Revenue milestones | Revenue-gated |
| Airdrop | 600,000 | 5% | Revenue milestones | Revenue-gated |
| Treasury | 7,800,000 - 8,400,000 | 65-70% | Governance-controlled | None |

### Pre-Crowdfund Distribution

Before the crowdfund opens, the following revenue-locked allocations are distributed and published:

**Team Allocation (1,800,000 ARM):** See Team Allocation section below for full specification. All assignments are revenue-locked and published — role labels, wallet addresses, and percentages — before the crowdfund opens.

**Airdrop Allocation (600,000 ARM):**
- Distributed to qualifying recipients before the crowdfund opens
- Revenue-locked (same terms as team)

**Transparency:**

Crowdfund participants can evaluate the full distribution (ie. who holds what and under what terms) before committing.

### Team Allocation

The team allocation (1,800,000 ARM, 15%) is distributed directly to individual locked wallets before the crowdfund opens, while ARM has no established market price. Each team member holds their own allocation independently, and there is no shared entity controlling the combined stake.

A fluid pool of unallocated ARM will be held in a Knowable multisig for future contributors, role top-ups, and core team positions not yet filled at crowdfund open. Distributions from the fluid pool are subject to the same revenue-lock terms as all team allocations.

**Properties:**
- Revenue-locked transferability (same schedule as all team/airdrop tokens)
- Voting power unlocks proportionally with revenue milestones
- Each team member governs independently ie. the team does not vote as a bloc

**Constraints:**
- Cannot change the revenue-lock schedule
- Cannot pull from treasury or other allocations
- Fluid pool distributions inherit the same unlock terms

### Revenue-Gated Unlocks (Team + Airdrop)

Team and airdrop tokens unlock and gain voting power only via cumulative protocol revenue:

| Cumulative Revenue | Unlocked | Voting Power |
|---|---|---|
| $0 | 0% | 0% |
| $10k | 10% | 10% |
| $50k | 25% | 25% |
| $100k | 40% | 40% |
| $250k | 60% | 60% |
| $500k | 80% | 80% |
| $1M | 100% | 100% |

Governance power follows economic commitment. Until the protocol generates revenue, only people who paid decide.

---

## Word-of-Mouth Whitelist

### Design Philosophy

This is a trusted-network crowdfund, not viral growth. Participation requires invitation from someone closer to the core. Hop-0 and hop-1 each have an enforced ceiling — the maximum fraction of the available pool each can absorb. Hop-2 has no enforced ceiling; instead it has a reserved floor (first claim on 5% of the fund) and an effective ceiling determined by the floor plus any hop-1 leftover. Actual allocation is demand-driven: each hop receives whatever it committed, up to its effective ceiling, and anything unused rolls forward to later hops. The launch team's invite budget closes after week 1; seeds and hop-1 invitees may invite throughout all three weeks; commitments remain open for the full three weeks.

### Initial Network Seeding

The people creating Armada are inviting their network of allies to help form the Armada community. Seeds, and the initial invitations from the launch team, are the first nodes in a trust network that extends outward through word of mouth.

Seed criteria are determined by the launch team based on demonstrated alignment, contribution, and long-term interest in privacy infrastructure.

**Maximum seed count: 150.** The total number of seeds is capped at 150. This bounds the maximum network size at approximately 1,740 participant slots (150 seed nodes + up to 510 hop-1 nodes + up to 1,080 hop-2 nodes). Arithmetic: hop-1 slots = (150 seeds × 3 invites) + 60 launch-team hop-1 = 510; hop-2 slots = (510 hop-1 nodes × 2 invites) + 60 launch-team hop-2 = 1,080. Because the same address may occupy multiple hop levels, the number of distinct individuals may be lower.

**Seed additions.** The launch team may add new seeds at any point during the first week, up to the 150-seed cap. Seeds added later in week 1 naturally have less of the 3-week commitment window remaining — that's the cost of late addition. Once added, a seed may issue invitations throughout the full three weeks.

**Launch team hop-1 invite budget.** In addition to seeds, the launch team holds a predeclared budget of 60 direct hop-1 placements, not tied to any seed slot. This allows the launch team to invite allies with direct relationships to hop-1 without creating phantom unfilled hop-0 slots. The budget is fixed for the duration of the crowdfund.

Launch team hop-1 invitees are full hop-1 participants: same cap ($4,000), same invite rights (up to 2 hop-2 invites each). They appear in the invite graph with the launch team address as inviter. In the `(address, hop)` node model, launch-team-issued invites originate from a designated `(launch_team_address, ROOT)` sentinel node — a fixed address with no hop level and no cap, whose sole role is to issue the predeclared launch team budget. This node is not a participant and makes no commitment.

**Launch team hop-2 invite budget.** The launch team also holds a predeclared budget of 60 direct hop-2 placements. The intended use case is friends, family, and adjacent supporters for whom the $1,000 cap is appropriate. These participants receive no invite rights (consistent with all hop-2 participants). Budget is fixed for the duration of the crowdfund.

**No financial incentive to invite.** Participants receive no allocation benefit from others' commitments. There is no referral bonus, no token reward, and no increase in a participant's own allocation from someone else's commitment. Invitations are an act of community-building, not a financial strategy. This keeps the invite graph honest: people invite those they genuinely want in the community, not those most likely to maximize their own return.

### Hop Structure

| Hop | Description | Ceiling | Floor | Cap | Invites |
|---|---|---|---|---|---|
| 0 | Seeds (max 150) | 70% | — | $15,000 | 3 |
| 1 | Seed invites + launch team invitations | 45% | — | $4,000 | 2 |
| 2 | Hop-1 invites + launch team invitations | — ¹ | 5% | $1,000 | 0 |

¹ Hop-2 has no enforced ceiling — only a floor. Its effective ceiling is `hop2_floor + hop1_leftover`, which can reach the full sale size if hop-0 and hop-1 are both empty. The 10% figure shown in some earlier drafts was removed as dead code (`HOP_CEILING_BPS` contains no entry for hop-2).

The launch team holds a predeclared invite budget separate from hop-0: 60 hop-1 invitations and 60 hop-2 invitations. These do not consume seed invite slots. Launch team invitations may only be issued during the first week.

Hop-2 has first claim on 5% of the fund before hop-0 and hop-1 ceilings are applied. This reserved capacity is always available to hop-2, regardless of earlier hop demand, but hop-2 only receives it to the extent hop-2 participants actually commit. Hop-0 and hop-1 ceilings are calculated against the fund net of this reservation. At base fund: $60k reserved for hop-2, hop-0 and hop-1 compete for the remaining $1.14M.

Hop-0 and hop-1 ceilings are overlapping by design — they sum to 115% of the available pool, not 100%. Hop-2 has no enforced ceiling; its effective ceiling is its reserved floor plus any hop-1 leftover, which can range from the floor to the full sale size. No hop is guaranteed to fill its ceiling. Each hop absorbs up to its effective ceiling based on actual demand; anything unused rolls forward. Earlier hops fill first; later hops absorb whatever remains.

### Ceiling Amounts

Hop-2 floor reservation comes off the top. Hop-0 and hop-1 ceilings are applied against the remainder.

**Base fund ($1.2M):**

| Hop | Floor reservation | Available pool | Ceiling (enforced) | Max ceiling (with rollover) |
|---|---|---|---|---|
| 2 | $60,000 | — | No enforced ceiling | $60k floor + full hop-1 leftover |
| 0 | — | $1,140,000 | 70% = $798k | $798k |
| 1 | — | $1,140,000 | 45% = $513k | $1,140k (if hop-0 empty) |

**Expanded fund ($1.8M):**

| Hop | Floor reservation | Available pool | Ceiling (enforced) | Max ceiling (with rollover) |
|---|---|---|---|---|
| 2 | $90,000 | — | No enforced ceiling | $90k floor + full hop-1 leftover |
| 0 | — | $1,710,000 | 70% = $1,197k | $1,197k |
| 1 | — | $1,710,000 | 45% = $769.5k | $1,710k (if hop-0 empty) |

### Invitation Limits

Invitation limits are hop-specific and predeclared:

- Hop-0 → may invite up to 3 addresses to hop-1
- Hop-1 → may invite up to 2 addresses to hop-2
- Hop-2 → may not invite

Invitations are signed on-chain (inviter → invitee, with hop level). The invite graph is defined over `(address, hop)` nodes, not bare addresses. This means:

- The same address may appear at multiple hop levels as a distinct node at each
- An invite edge connects `(inviter_address, inviter_hop)` → `(invitee_address, invitee_hop)`
- A seed (hop-0) inviting their own address to hop-1 creates a self-loop edge, visible as such in the graph
- Invite slot consumption is per `(address, hop)` node: a hop-0 node has 3 outgoing invite slots; a hop-1 node has 2. A single address occupying both hop-0 and hop-1 has both slot budgets, but they are consumed independently by each node. A natural consequence: a seed who self-invites their own address to hop-1 may then use that hop-1 node's 2 invite slots to invite 2 additional hop-2 addresses — giving one address unilateral control over a 1→1→2 subtree, fully legible in the graph. For full entity-level capture analysis including the complete 1→3→6 subtree, see Self-Filling section.
- For allocation, each `(address, hop)` node participates independently — an address at hop-0 and hop-1 receives ARM from both hop buckets, accumulated into a single address balance

This model makes entity-level participation unambiguously legible without wallet correlation. Separate-wallet self-fill is also permitted and visible in graph shape, but same-entity control across wallets is not provable on-chain.

**An address may appear in multiple independent subtrees.** There is no constraint preventing address_a from being a seed in their own right (hop-0) while also being invited by an unrelated seed to hop-1 or hop-2. Each `(address, hop)` node participates in allocation independently, so address_a accumulates ARM from every position it holds across all subtrees. All such participation is visible in the graph — every edge is public — so participants can observe multi-subtree presence directly. This is a deliberate choice: we cannot prevent it, and allowing the same address makes concentration legible rather than hidden.

### Self-Filling and Max Single-Entity Capture

A participant may commit at multiple hop levels using a single address, and receives ARM allocation from each hop they participate in — subject to that hop's cap and pro-rata rules. This is the preferred model for transparency: a seed participating at hop-0, hop-1, and hop-2 with the same address creates an unambiguously legible self-loop in the invite graph, and receives their full multi-hop allocation without any penalty.

Participating across hops using separate wallets is also permitted. Graph edges are visible, but same-entity control across separate wallets cannot be proven on-chain.

The maximum a single entity can capture **from their own subtree** is **$33,000**. An address invited by multiple independent seeds accumulates beyond this — each additional position they hold in another subtree adds independently. All such participation is visible in the graph. A seed filling all their invite slots:

| Position | Count | Cap | Max USDC |
|----------|-------|-----|----------|
| Hop-0 (own address) | 1 | $15,000 | $15,000 |
| Hop-1 (own address, self-invited) | 1 | $4,000 | $4,000 |
| Hop-1 (separate wallets, 2 remaining hop-0 slots) | 2 | $4,000 | $8,000 |
| Hop-2 (own address, self-invited from hop-1) | 1 | $1,000 | $1,000 |
| Hop-2 (from own hop-1 node's remaining slot) | 1 | $1,000 | $1,000 |
| Hop-2 (from 2 external hop-1 wallets × 2 slots each) | 4 | $1,000 | $4,000 |
| **Total** | **10 positions, 8 addresses** | | **$33,000** |

Using the same address at hop-0, hop-1, and hop-2 reduces the number of wallets needed (8 instead of 10) but does not change the $33k ceiling. $33,000 out of a $1.2M base fund is 2.75%.

The actual deterrents to governance concentration remain:

1. **Real capital required at every position.** $33k fully deployed is the maximum; it requires real capital across all positions.
2. **Cap differential.** Hop-0 gets $15k, hop-1 gets $4k, hop-2 gets $1k. The incremental benefit of filling lower hops is real but bounded.
3. **Graph visibility.** The full invite graph is public in real time. Self-loop chains and multi-wallet chains are both visible in structure.
4. **Governance concentration has low ROI pre-revenue.** Accumulating votes in a protocol with no product yet is not obviously valuable.

Residual risk — a seed concentrating votes at scale — is primarily mitigated by trusted-network seed selection. The mechanism does not make it impossible; it makes it expensive, visible, and bounded.

---

## Crowdfund Mechanics

### Timeline

| Phase | Duration |
|---|---|
| Launch team invite window | Days 1–7 (first week only) |
| Seed invite window | Days 1–21 (full 3 weeks) |
| Hop-1 invite window | Days 1–21 (full 3 weeks) |
| Commitment window | Days 1–21 (full 3 weeks) |

The launch team's invite budget (seeds, hop-1, hop-2 placements) may only be issued during the first week. Seeds and hop-1 participants may issue invitations throughout the full three weeks — right up to the commitment deadline. This limits the launch team's ability to issue new invitations with live oversubscription data visible (they can still do so during days 1–7 but not after), while giving seeds and hop-1 invitees maximum flexibility to propagate the network throughout the window. Note: a launch team hop-1 invitee issued on day 7 has the full remaining 14 days to issue their own hop-2 invitations — the launch team's invite window closing does not affect hop-1 invite rights.

### Commitment

- **Currency:** USDC
- **Method:** Commit to escrow contract
- **Multi-hop commits from the same address are permitted.** An address may commit at multiple hop levels and receives ARM allocation from each hop it participates in. This makes entity-level participation unambiguously legible on-chain: a seed who also fills hop-1 and hop-2 slots with the same address is visibly doing so, and receives the ARM from all three pools.
- **Multiple commitments:** A participant may commit multiple times to any hop. Commitments from the same address at the same hop are aggregated into a single running balance, capped at the hop cap for allocation purposes.
- **Visibility:** Address, amount, hop, and invite edges are all public in real time
- **No withdrawals:** Commitments are final once submitted. Participants cannot withdraw USDC before the deadline. The commitment window is a maximum of 3 weeks; participants should only commit amounts they are prepared to lock for that period.

Real-time aggregate statistics show current commitment levels per hop throughout the window, allowing participants to observe oversubscription before committing. **Per-hop totals are the canonical display** — each hop shows the effective demand committed to that hop, regardless of whether any addresses also participate at other hops. An address committed at hop-0 and hop-1 contributes to both hop totals independently. This means the sum of all displayed per-hop totals may exceed the sale size, which is expected and correct — it reflects genuine multi-hop demand that will be allocated and scaled per hop.

### ARM Pre-Load Requirement

**1,800,000 ARM (MAX_SALE) must be transferred to the crowdfund contract before the commitment window opens.** The contract enforces this with a deployment-time flag — the commitment function reverts until ARM is loaded and verified. This ensures participant claim records written at finalization are always backed by sufficient ARM.

Deployment sequence:
1. Deploy `ArmadaCrowdfund` with treasury address, ARM address, timing parameters
2. Transfer 1,800,000 ARM to the contract
3. Call `loadArm()` to verify balance and set the flag
4. Commitment window opens

### Finalization

After the commitment window closes, finalization is permissionless — anyone may call `finalize()` once both conditions are met:

- The commitment deadline has passed, **and**
- `capped_demand` (sum of all effective per-address per-hop balances after caps — an address committed at multiple hops contributes at each hop independently) meets or exceeds the minimum raise of **$1,000,000 USDC**

If the commitment deadline passes without `capped_demand` meeting the minimum raise, the contract does not self-execute. `claimRefund()` derives eligibility from state. A participant may withdraw their full deposited USDC if and only if **any** of these conditions hold:

1. `refundMode == true` — set by the post-allocation check in `finalize()` when net_proceeds < MINIMUM_RAISE
2. `block.timestamp > commitmentDeadline && !finalized && capped_demand < MINIMUM_RAISE` — deadline passed without a qualifying raise. The `capped_demand < MINIMUM_RAISE` clause is critical: without it, a qualifying crowdfund that simply hasn't been finalized yet would incorrectly permit refunds.
3. `cancelled == true` — Security Council invoked `cancel()`

No privileged action or separate function is required — the eligibility check is built into `claimRefund()` itself.

When `finalize()` is called:

> These steps describe settlement outcomes, not mandatory EVM instruction order. Implementation must follow checks-effects-interactions: record all state changes before making external asset transfers.

1. Crowdfund size is determined (base or expanded, based on total capped demand)
2. Per-hop ceilings are calculated
3. Allocations are computed (pro-rata where oversubscribed; addresses at multiple hops receive ARM from each hop independently, accumulated into a single balance)
4. Refund amounts are computed and recorded per address
5. **Post-allocation minimum raise check:** if `net_proceeds < MINIMUM_RAISE ($1,000,000 USDC)`, the contract sets **both `finalized = true` and `refundMode = true`** and stops — no treasury transfer, no ARM allocation recording. Setting `finalized = true` permanently disables further calls to `finalize()` and `cancel()`. Any provisional allocation and refund amounts computed in steps 3–4 are discarded. **In this branch, every participant may call `claimRefund()` to withdraw their full deposited USDC** — not a pro-rata refund, but the complete raw amount they deposited. **`claim()` reverts when `refundMode == true` — no ARM allocations were recorded.** This scenario can occur at base size when hop-0 is oversubscribed and combined later-hop demand doesn't close the gap between hop-0's ceiling ($798k) and the minimum raise ($1M). It cannot occur after expansion, where hop-0's ceiling ($1,197k) already exceeds the minimum raise. This must not revert the transaction — a revert would leave the contract permanently stuck with no path to refunds.
6. **Net USDC proceeds transfer immediately to treasury** — refund amounts are deducted first; only the net proceeds transfer
7. ARM allocations are recorded on-chain for participants to claim

Refunds are pull-based — participants call `claimRefund()` once eligible (see Finalization section for the full three-condition eligibility rule, which includes pre-finalization and cancel-state paths). This avoids gas grief from iterating over all participants in a single transaction. Refunds do not expire.

ARM claim remains a separate participant-initiated step (one transaction per claimant). USDC net proceeds flow to treasury at finalization regardless of whether participants have claimed their ARM or refunds.

Crowdfund tokens are not revenue-gated, but voting power only becomes active once ARM is claimed and delegated. Allocated-but-unclaimed ARM is vote-inert regardless of any governance unlock proposal that passes.

**Governance quiet period: 7 days after finalization.** No governance proposals may be submitted until day 8 post-finalization. This gives the community time to claim, delegate, and orient before governance begins. Any emergency during this window is handled by the Security Council (pause, veto, coordinated response) — it does not require a governance vote. The quiet period is a one-time bootstrapping measure and is itself governable.

**Early-claim governance risk.** Voting power uses Compound-style point-in-time checkpointing — there is no time-weighted average. An address that claims and delegates immediately after finalization has its full balance active with no averaging period. Quorum at proposal creation is measured against only the ARM that has been claimed and delegated by that block. If very few participants have claimed, a single early claimer with 12,000+ ARM could meet proposal threshold and create a vote where quorum is a fraction of the full crowdfund supply. The 48-hour proposal delay and 7-day quiet period are the only buffers. Participants are strongly encouraged to claim and delegate promptly after finalization.

**Claim deadline: 3 years from finalization.** The deadline is fixed and cannot be extended — it is a predeclared term of participation, not a governance parameter.

**`withdrawUnallocatedArm()` eligibility — three windows:**
- **Immediately post-finalization:** sweeps `MAX_SALE - allocated_arm` — all ARM not sold to any participant, including (in a base crowdfund) the 600,000 ARM preloaded for potential expansion that was never needed.
- **After the 3-year claim deadline:** sweeps any ARM still in the contract — allocated-but-never-claimed participant allocations.
- **After `cancel()`:** sweeps all preloaded ARM (1,800,000) — the crowdfund is in cancelled state, not finalized, so the function must check `cancelled` as an additional eligibility condition alongside `finalized`.

All three send to the immutable treasury address. The function may be called at any time; it sweeps whatever is currently eligible under the above rules.

**Commitment deadline is contract-enforced.** The contract reverts any commitment transaction submitted after the deadline timestamp. This is a hard contract-level guard, not a UI convention.

Post-finalization, all privileged functions are permanently inactive. `withdrawUnallocatedArm()` is permissionless — anyone may call it, and ARM transfers to the immutable treasury address set at deployment. No privileged role survives finalization.

### Allocation

The allocation algorithm proceeds in four sequential steps, each with a separately bounded budget:

1. **Hop-2 floor reserved first.** 5% of `sale_size` is set aside for hop-2 before hop-0 or hop-1 are allocated. Hop-0 and hop-1 compete only for the remaining `available` pool.

2. **Hop-0 allocated from `available`.** Demand is served up to `hop0_ceiling`. Any unused hop-0 capacity increases hop-1's effective ceiling.

3. **Hop-1 allocated from remaining available.** Hop-1's effective ceiling = `min(hop1_ceiling + hop0_leftover, remaining_available)`. Bounded by what remains after hop-0.

4. **Hop-2 allocated from floor + hop-1 leftover.** Hop-2's effective ceiling = `hop2_floor + hop1_leftover`. The floor is always available regardless of earlier hop demand. Any capacity hop-2 doesn't absorb becomes unsold ARM, recoverable via `withdrawUnallocatedArm()`.

Oversubscription at any hop is resolved pro-rata within that hop's effective ceiling, bounded by per-address caps. An address committed at multiple hops receives ARM allocation from each hop independently. See appendix for full pseudocode.

`capped_demand` — the sum of all aggregated per-address per-hop balances after caps — is the canonical demand variable used for the minimum raise check, the expansion trigger, and per-hop statistics. An address committed at multiple hops contributes to `capped_demand` at each hop independently.

### Rollover

Rollover is integrated into the allocation steps rather than a separate pass:

- Hop-0 leftover increases hop-1's effective ceiling (bounded by what remains after hop-0)
- Hop-1 leftover increases hop-2's effective ceiling (stacks on top of the guaranteed floor)
- Hop-2 leftover becomes unsold ARM, recoverable via `withdrawUnallocatedArm()`

Leftover never rolls back to earlier hops.

### Elastic Expansion

The crowdfund expands if `capped_demand` reaches or exceeds the expansion trigger.

```python
if capped_demand >= EXPANSION_TRIGGER:  # $1.5M USDC capped demand
    sale_size = MAX_SALE  # 1,800,000 ARM ($1.8M, 15% of supply)
else:
    sale_size = BASE_SALE  # 1,200,000 ARM ($1.2M, 10% of supply)
```

Expansion is binary: either base ($1.2M) or max ($1.8M). If capped demand clears $1.5M, supply expands to maximum. Oversubscribed hops are allocated pro-rata up to their effective ceiling; the difference between a participant's commitment and their allocation is refunded.

### Refunds

```
refund_usdc = total_deposited_usdc - (allocation_arm × price)
```

Refunds are pull-based — participants call `claimRefund()` once eligible. See Finalization section for the full eligibility conditions.

---

## Emergency Cancel

The crowdfund contract includes an emergency cancel mechanism for catastrophic pre-finalization scenarios: a critical contract bug, a regulatory injunction, or another event that would make proceeding harmful to participants.

**Authority:** Security Council (3-of-5 multisig — see GOVERNANCE.md)

**Process:**
1. Security Council invokes `cancel()` — takes effect immediately
2. Contract enters refund-only mode: all committed USDC becomes withdrawable by participants via `claimRefund()`. No further privileged action required — participants pull their own refunds.

**`cancel()` sets a permanent cancelled state.** After `cancel()` is invoked: `finalize()` permanently reverts (the cancelled flag is checked at entry); `commit()` permanently reverts — no new USDC may be deposited; participants may call `claimRefund()` to withdraw their full deposited USDC; and all preloaded ARM (1,800,000) becomes immediately sweepable to the immutable treasury address via `withdrawUnallocatedArm()`. The cancelled state is irreversible.

**Constraints:**
- Only callable before `finalize()` has been executed
- Once finalized, `cancel()` permanently reverts — no post-finalization admin leverage
- Refund mode is automatic and unconditional; the Security Council has no discretion over individual refunds

**Rationale:** Cancel takes effect immediately because a genuine emergency (active exploit, regulatory injunction) requires speed — a delay serves the attacker, not participants. Abuse protection comes from Security Council composition (not purely team-controlled), automatic unconditional refunds (no discretion over destination), and the pre-finalization-only restriction. The on-chain transaction is public the moment it is submitted.

---

## Governance

Crowdfund tokens are not revenue-gated. Voting power becomes active once ARM is claimed and delegated — unclaimed ARM is vote-inert. Team and airdrop tokens gain voting power proportional to revenue-milestone unlocks — at $0 protocol revenue, only crowdfund participants who have claimed and delegated vote.

ARM must be delegated to vote. Delegation is designated at claim time as a mandatory parameter.

Standard proposals take approximately 11 days (48h delay + 7d vote + 48h execution). High-impact proposals (fee changes, treasury >5%, governance parameters, steward elections) take approximately 18 days (48h + 14d + 7d). Bond: 1,000 ARM, returned after lock period. Proposal threshold: 12,000 ARM.

Crowdfund tokens become transferable via governance proposal. There are no predeclared conditions — holders decide when.

Team and airdrop tokens cannot be unlocked by governance. They unlock only via revenue milestones.

Treasury tokens (65–70%) are controlled by governance but do not vote.

See GOVERNANCE.md for full specification: voting mechanics, delegation, quorum, proposal lifecycle, Treasury Steward, Security Council, and wind-down.

---

## Invite Graph Visibility

The full invite graph is public throughout the crowdfund — inviter, invitee, and hop level are visible from the moment an invite is issued. There is no post-finalization reveal step.

Participants are joining a trust network, and that network's shape should be legible to everyone in it. Full visibility during the crowdfund also reinforces the governance bootstrapping framing: you can see who you're building the network with before committing.

---

## Wind-Down

### Automatic wind-down

The protocol triggers an automatic wind-down if the revenue threshold is not reached by the deadline. Both parameters are governable with the following defaults:

| Parameter | Default | Governable |
|---|---|---|
| Revenue threshold | $10,000 cumulative | Yes |
| Deadline | December 31, 2026 | Yes |

The intent is to give the protocol approximately 6 months after mainnet launch to demonstrate traction. December 31, 2026 is the concrete implementation default and is calibrated to that intent based on the current launch timeline. If launch slips materially, governance should update the deadline before it becomes binding.

If cumulative protocol revenue does not reach the threshold by the deadline, wind-down initiates automatically — no governance vote required. Governance may adjust either parameter before the deadline, or vote to wind down early at any time.

### Wind-down sequence

1. Shielded pool enters withdraw-only mode (immediate — `shield()` and `transfer()` disabled; `unshield()` always available indefinitely with no deadline)
2. 30-day waiting period before treasury distribution snapshot — not a deadline for unshielding, but a window for participants to get their affairs in order before the distribution is calculated
3. Treasury distribution snapshot taken. Pro-rata entitlements are recorded. **All allocated crowdfund ARM counts** — both claimed and unclaimed — so unclaimed participants are not disadvantaged. ARM transfers remain unrestricted after wind-down.
4. ARM holders claim their pro-rata share of non-ARM treasury assets (USDC, ETH, and any other tokens held). Participants who haven't yet claimed ARM may do so after wind-down and then separately claim their treasury entitlement. The 3-year ARM claim deadline continues to apply.
5. Team and airdrop tokens have no treasury claim unless unlocked by revenue milestones before wind-down (zero-cost-basis exclusion for locked tokens)
6. Treasury ARM has no distribution mechanism after wind-down — it remains locked permanently

**Unclaimed ARM after wind-down.** Participants may claim their ARM at any time after wind-down (subject to the 3-year deadline). Claiming after wind-down is harmless — it simply arrives after the treasury distribution snapshot, but the participant's treasury entitlement is already recorded and claimable separately. No participant is penalised for claiming late.

Those who paid have priority over those who received tokens at zero cost basis. Locked tokens only unlock if protocol earns revenue — if the protocol failed before earning revenue, those tokens stay locked and have no claim on remaining assets.

---

## Risk Acknowledgments

- **Seed quality is the critical dependency.** The allocation mechanism is algorithmic, but the network's governance quality depends entirely on the launch team's seed selection. Trusted-network seed selection is the primary defence against governance concentration and Sybil attacks — the contract mechanism does not substitute for it. Maximum single-entity capture from one subtree is $33k (2.75% of base fund); an address invited by multiple seeds accumulates beyond this. See Self-Filling section.
- **At base size, hop-0 alone cannot meet the minimum raise.** Hop-0's ceiling ($798k at base fund) is below the minimum raise ($1M). However, the same addresses filling hop-0 can also commit at hop-1 and hop-2 — those contributions count independently. For example, 67 seeds each committing $15k at hop-0 and $4k at hop-1 produces $1.273M capped_demand (base sale) and ~$1.066M net_proceeds — sufficient to finalize. After expansion (capped_demand ≥ $1.5M), hop-0's ceiling rises to $1,197k and hop-0 alone can succeed.
- **Early governance proposals may pass on thin quorum.** Voting power activates immediately upon claim and delegation with no averaging period. The first proposals after the 7-day quiet period may be created when only a small fraction of participants have claimed. Participants are strongly encouraged to claim and delegate promptly after finalization.
- **Governance capture is possible.** Whitelist is the mitigation, not a guarantee.
- **Propagation may fail.** Unused supply flows to treasury.
- **This is a trusted-network crowdfund.** Not designed for viral distribution.
- **Tokens are non-transferable at launch by design.** Transferability prioritizes governance participation during the formative phase. Token transfers can be unlocked by governance proposal.
- **Automatic wind-down is a real risk.** If the protocol does not reach $10k revenue by December 31, 2026, wind-down initiates automatically.

---

## Summary of Design Decisions

| Decision | Resolution | Rationale |
|---|---|---|
| Treasury voting | Treasury does not vote | Avoids control ambiguity |
| Team/airdrop voting | Revenue-gated | Only people who paid decide until protocol proves itself |
| Steward voting eligibility | Voting-eligible ARM only | Treasury decisions by economic stakeholders |
| Invite graph | Fully public throughout the crowdfund | No post-finalization reveal step. Participants are joining a trust network — its shape should be legible from the start. Simplifies implementation; reinforces governance bootstrapping framing over token allocation framing |
| Enactment delay | 48 hours standard; 7 days extended | Short delay is honest — without locking, market prices outcomes during voting anyway. Extended delay gives Security Council a meaningful veto window on high-impact proposals. |
| Elastic expansion | Binary to 1.8M ARM | Accommodates demand without distorting any future fundraising |
| Team allocation control | Individual locked wallets; fluid multisig for remainder | Individual ownership gives each team member independent governance standing; fluid pool preserves flexibility for future contributors without undermining $0 cost basis tax positioning |
| Pre-crowdfund distribution | Team + airdrop distributed before the crowdfund opens | Full transparency for funders |
| Recurring payments | Not supported in v1 | Steward submits monthly batches instead |
| Hop allocation model | Demand-driven ceilings, not fixed reserves | Capacity follows actual demand; no hop is guaranteed allocation it didn't earn; unused capacity rolls forward |
| Overlapping ceilings | Hop-0 and hop-1 sum to 115% of available (70/45); hop-2 has no fixed ceiling | Hop-0 and hop-1 can absorb more if the other underperforms. Hop-2's effective ceiling is floor + hop-1 leftover — it can grow arbitrarily large if earlier hops are empty, preserving full rollover flexibility |
| Hop-2 hard floor | 5% of the fund reserved — hop-2 has first claim on this capacity | Hop-2 is governance breadth and ecosystem signal. The reserved capacity ensures hop-2 can always absorb up to the floor amount regardless of earlier hop demand. Actual receipt depends on hop-2 demand. |
| Multi-hop commits from single address | Permitted; receives ARM allocation at each hop independently | $33k per-subtree ceiling (2.75% of base raise). An address invited by multiple seeds accumulates across all subtrees — no global cap, but all participation is visible in the graph. Concentration is legible, not preventable. |
| Invite window | Launch team: days 1–7 only; Seeds and hop-1: full 3 weeks | Narrows launch team discretion to week 1 — after that, no new seeds can be added and no new launch team invites can be issued. Live oversubscription data is visible during days 1–7, so this reduces but does not eliminate data-informed invite issuance. Seeds and hop-1 invitees retain full flexibility throughout. |
| Rolling seed additions | Launch team may add seeds during week 1 only, up to 150-seed cap | Constrained to launch team's invite window. Once added, seeds may invite throughout the full 3 weeks. |
| Seed cap | 150 seeds maximum | Bounds network to ~1,740 participant slots (nodes); distinct individuals may be fewer under single-address multi-hop |
| Launch team invite budgets | 60 hop-1 + 60 hop-2 invitations, predeclared, week 1 only | Seeds the initial community with allies not connected to any seed. |
| Rollover | Unconditional — leftover always rolls forward | Rollover thresholds dropped as weak sybil protection; seed selection is the real defence. Simpler contract. |
| No commitment withdrawals | Commitments are final once submitted | Eliminates gaming risk and simplifies contract; 3-week maximum lock period is known upfront |
| ARM pre-load requirement | 1,800,000 ARM loaded before window opens | Ensures claim records written at finalization are always backed by sufficient ARM; enforced by contract flag |
| Settlement model | Pull-based for refunds and ARM; push for net proceeds to treasury | `finalize()` records allocations and refund amounts, transfers only net proceeds to treasury. Participants pull refunds via `claimRefund()` and ARM via `claim()`. Avoids gas grief from iterating over all participants in one transaction. |
| Crowdfund finalization | Permissionless — callable by anyone after deadline + minimum raise met | No identified party triggers finalization. If deadline passes without minimum raise, `claimRefund()` derives eligibility from timestamp + state; no separate function or privileged action needed. |
| Crowdfund emergency cancel | Security Council (3-of-5 multisig), immediate effect, pre-finalization only | Speed serves participants in a genuine emergency. Abuse protection from Security Council composition, automatic unconditional refunds, and pre-finalization-only restriction — not a delay. |
| Post-finalization | No privileged roles | All privileged functions permanently inactive after finalization. `withdrawUnallocatedArm` is permissionless — anyone calls it, ARM goes to immutable treasury address. |
| Governance quiet period | 7 days after finalization; no proposals until day 8 | Gives community time to claim, delegate, and orient. Security Council handles any emergency during this window. Governable. |
| ARM claim deadline | 3 years from finalization, fixed | Predeclared term of participation, ungovernable by design |
| Automatic wind-down | Revenue threshold + deadline, both governable | Removes need for governance vote to initiate failure-case wind-down; defaults ($10k by Dec 31 2026) are conservative enough to signal genuine traction |

---

## Appendix: Allocation Pseudocode

The following pseudocode is the **specification-intent** description of the allocation algorithm. It uses simplified arithmetic for readability. The contract implementation must use **integer math throughout**: percentages as basis points (BPS), token amounts in their smallest denomination (USDC: 6 decimals, ARM: 18 decimals), floor rounding on all pro-rata splits (participants may receive fractionally less than their exact share; dust accumulates as unallocated ARM), and no floating-point operations.

**Two clean asset invariants the implementation must satisfy:**
- **USDC:** `net_proceeds + sum(refunds) == sum(total_deposited)` — all deposited USDC is either sent to treasury or returned to participants; none is stranded
- **ARM:** `allocated_arm + unsold_arm == MAX_SALE` — all pre-loaded ARM (always 1,800,000) is either allocated to participants or immediately recoverable as unsold capacity

**Contract preconditions (enforced before `finalize()` is called):**
- An address may commit at multiple hop levels and receives ARM allocation from each independently.
- `loadArm()` has been called; contract holds ≥ `MAX_SALE` ARM.
- The commitment deadline has passed and `capped_demand >= MINIMUM_RAISE`.
- After computing allocations, if `net_proceeds < MINIMUM_RAISE`, the contract sets both `finalized = true` and `refundMode = true` without reverting — `finalized = true` permanently disables `finalize()` and `cancel()`; `claimRefund()` activates via the `refundMode` condition. This can occur at base size when hop-0 is oversubscribed and combined later-hop demand doesn't close the gap to $1M. Cannot occur after expansion.

```python
# Constants
TOTAL_SUPPLY      = 12_000_000
BASE_SALE         = 1_200_000       # ARM (= USDC at $1/ARM)
MAX_SALE          = 1_800_000       # ARM
MINIMUM_RAISE     = 1_000_000       # USDC — checked against both capped_demand and net_proceeds
EXPANSION_TRIGGER = 1_500_000       # USDC capped demand threshold
PRICE             = 1               # Specification uses whole-unit amounts for clarity.
# Integer implementation: 1 whole ARM = 1e18 ARM units; 1 USDC = 1e6 USDC units.
# Price ratio: 1e6 USDC units per 1e18 ARM units (i.e. 1e-12 USDC units per ARM unit).
# All token amounts in the contract must use smallest units; this pseudocode uses
# whole units throughout for readability. Do not read PRICE = 1 as a unit conversion.

HOP_CEILING_BPS = {0: 7000, 1: 4500}    # BPS of available pool; hop-2 uses floor+rollover, not a BPS ceiling
HOP2_FLOOR_BPS  = 500                   # BPS of sale_size
HOP_CAP         = {0: 15_000, 1: 4_000, 2: 1_000}  # USDC per address per hop


def allocate(commitments):
    """
    commitments: list of Commitment(address, hop, usdc_committed)
    An address may commit at multiple hops and receives ARM allocation from each.
    Deposits above the hop cap are permitted; excess is refunded at settlement.
    """

    # Step 1: Aggregate multiple commitments per (address, hop), capped at hop cap.
    aggregated = {}  # (address, hop) -> effective USDC (capped at hop cap)
    for c in commitments:
        key = (c.address, c.hop)
        aggregated[key] = min(
            aggregated.get(key, 0) + c.usdc_committed,
            HOP_CAP[c.hop]
        )

    # Step 2: Compute capped_demand and determine sale size.
    # capped_demand = sum across ALL (address, hop) pairs — each hop counted independently.
    capped_demand = sum(aggregated.values())  # USDC
    sale_size = MAX_SALE if capped_demand >= EXPANSION_TRIGGER else BASE_SALE

    # Step 3: Reserve hop-2 floor upfront.
    # Hop-2 has first claim on this capacity; hop-0 and hop-1 compete only for available.
    hop2_floor   = HOP2_FLOOR_BPS * sale_size // 10_000
    available    = sale_size - hop2_floor

    hop0_ceiling = HOP_CEILING_BPS[0] * available // 10_000
    hop1_ceiling = HOP_CEILING_BPS[1] * available // 10_000

    def participants(hop):
        # All addresses committed at this hop, regardless of other hops they joined.
        return {addr: amt for (addr, h), amt in aggregated.items() if h == hop}

    allocations = {}  # address -> total ARM allocated (accumulated across all hops)

    # Step 4: Allocate hop-0 from available pool.
    p0 = participants(0)
    d0 = sum(p0.values())
    if d0 <= hop0_ceiling:
        for addr, amt in p0.items():
            allocations[addr] = allocations.get(addr, 0) + amt // PRICE
        hop0_leftover = hop0_ceiling - d0
        eff0 = d0
    else:
        for addr, amt in p0.items():
            allocations[addr] = allocations.get(addr, 0) + amt * hop0_ceiling // d0 // PRICE  # floor round
        hop0_leftover = 0
        eff0 = hop0_ceiling

    remaining_available = available - eff0

    # Step 5: Allocate hop-1 from remaining available pool.
    # Hop-0 leftover increases hop-1's effective ceiling,
    # but hop-1 can never absorb more than remaining_available.
    hop1_eff_ceiling = min(hop1_ceiling + hop0_leftover, remaining_available)
    p1 = participants(1)
    d1 = sum(p1.values())
    if d1 <= hop1_eff_ceiling:
        for addr, amt in p1.items():
            allocations[addr] = allocations.get(addr, 0) + amt // PRICE
        hop1_leftover = hop1_eff_ceiling - d1
        eff1 = d1
    else:
        for addr, amt in p1.items():
            allocations[addr] = allocations.get(addr, 0) + amt * hop1_eff_ceiling // d1 // PRICE  # floor round
        hop1_leftover = 0
        eff1 = hop1_eff_ceiling

    # Step 6: Allocate hop-2 from floor + any hop-1 leftover.
    # Floor capacity is always available (reserved in Step 3).
    hop2_eff_ceiling = hop2_floor + hop1_leftover
    p2 = participants(2)
    d2 = sum(p2.values())
    if d2 <= hop2_eff_ceiling:
        for addr, amt in p2.items():
            allocations[addr] = allocations.get(addr, 0) + amt // PRICE
        eff2 = d2
    else:
        for addr, amt in p2.items():
            allocations[addr] = allocations.get(addr, 0) + amt * hop2_eff_ceiling // d2 // PRICE  # floor round
        eff2 = hop2_eff_ceiling

    # Step 7: Compute USDC and ARM accounting.
    #
    # USDC accounting:
    #   net_proceeds  = USDC actually sold = ARM allocated × PRICE (NOT eff0+eff1+eff2,
    #                   which overcounts dust from floor rounding)
    #   refunds       = deposited USDC not allocated → claimable via claimRefund()
    #   invariant:    net_proceeds + sum(refunds) == sum(total_deposited)
    #
    # ARM accounting:
    #   allocated_arm = ARM owed to participants → claimable via claim()
    #   unsold_arm    = ARM never allocated → claimable via withdrawUnallocatedArm() immediately
    #   invariant:    allocated_arm + unsold_arm == MAX_SALE
    #   (Allocated-but-unclaimed ARM after 3-year deadline also swept by withdrawUnallocatedArm())

    allocated_arm = sum(allocations.values())                  # ARM
    net_proceeds  = allocated_arm * PRICE                      # USDC (floor-rounding dust stays in contract as unsold_arm)
    unsold_arm    = MAX_SALE - allocated_arm                   # ARM — always uses MAX_SALE, not sale_size
    # 1,800,000 ARM is preloaded regardless of whether the sale expands.
    # In a base sale, the 600,000 expansion reserve is also immediately sweepable.

    # Post-allocation minimum raise check.
    # IMPORTANT: must NOT revert. If net_proceeds < MINIMUM_RAISE, set refundMode = true.
    # All provisional allocation/refund calculations above are DISCARDED.
    # claimRefund() in this branch returns each address's full raw deposited USDC.
    if net_proceeds < MINIMUM_RAISE:
        total_deposited = {}
        for c in commitments:
            total_deposited[c.address] = total_deposited.get(c.address, 0) + c.usdc_committed
        # refundMode = true: each address claims total_deposited[address], no ARM allocated
        full_refunds = {addr: deposited for addr, deposited in total_deposited.items()}
        # Verify: sum(full_refunds) == sum(total_deposited); no ARM allocated
        assert sum(full_refunds.values()) == sum(total_deposited.values())
        return None, full_refunds, MAX_SALE, 0  # (no allocations, full refunds, all ARM unsold, zero proceeds)

    total_deposited = {}
    for c in commitments:
        total_deposited[c.address] = total_deposited.get(c.address, 0) + c.usdc_committed

    refunds = {}
    for addr, deposited in total_deposited.items():
        allocated_usdc = allocations.get(addr, 0) * PRICE
        refunds[addr] = deposited - allocated_usdc  # always >= 0

    # Verify invariants (implementation must enforce these):
    assert net_proceeds + sum(refunds.values()) == sum(total_deposited.values())  # USDC
    assert allocated_arm + unsold_arm == MAX_SALE                                  # ARM

    return allocations, refunds, unsold_arm, net_proceeds
```

**Key properties the implementation must preserve:**

| Property | Guarantee |
|---|---|
| Hop-2 reserved capacity | Hop-2 always has first claim on `HOP2_FLOOR_BPS × sale_size`; actual receipt depends on hop-2 demand |
| USDC conservation | `net_proceeds + sum(refunds) == sum(total_deposited)` |
| ARM conservation | `allocated_arm + unsold_arm == MAX_SALE` (1,800,000 always preloaded; base-sale expansion reserve is immediately sweepable) |
| Minimum raise | If `net_proceeds < MINIMUM_RAISE` after allocation, contract sets `refundMode = true` (no revert — state must be preserved for `claimRefund()` to work) |
| Integer rounding | All pro-rata splits floor-rounded; dust accumulates in `unsold_arm` |

**Asset flows post-finalization:**

| Asset | When | Mechanism |
|---|---|---|
| Net USDC proceeds | At finalization | Push to immutable treasury address |
| Excess USDC (refunds) | After finalization, no expiry | Pull: participant calls `claimRefund()` |
| Unsold ARM | After finalization | Pull: anyone calls `withdrawUnallocatedArm()` |
| Allocated-but-unclaimed ARM | After 3-year claim deadline | Pull: anyone calls `withdrawUnallocatedArm()` (sweeps remaining ARM balance at that point) |
