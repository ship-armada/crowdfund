Here’s a **v0.1 Implementation Test Spec** you can hand to Ian.

It is written as a contract-facing test plan, not a marketing or review doc. It assumes **`CROWDFUND.md` is canonical**, while the observer and committer specs define event/interface compatibility the implementation must preserve.   

---

# Armada Crowdfund — Implementation Test Spec (v0.1)

## 1. Purpose

Verify that the contract implementation matches the canonical crowdfund mechanism exactly in:

* state transitions
* accounting invariants
* allocation / refund behavior
* invite / slot mechanics
* lazy settlement (aggregate finalization + per-user claim-time computation)
* emitted event surface required by the observer and committer UIs

This test spec is the bridge between `CROWDFUND.md` and the eventual Foundry test suite. The implementation is not ready for audit until all tests and invariants in this document pass.  

## 2. Sources of Truth

### Canonical contract source

* `CROWDFUND.md` — mechanism, state machine, contract interface, allocation logic, settlement logic, event semantics. 

### Contract-surface compatibility sources

* `CROWDFUND_OBSERVER.md` — event-consumed read model, per-hop settlement display assumptions, `computeAllocation()` view function expectations. 
* `CROWDFUND_COMMITTER.md` — claim timing assumptions, invite-link lifecycle expectations, `computeAllocation()` for pre-claim display. 

### Review / traceability source

* `CROWDFUND_REVIEW_BRIEF.md` — state-machine completeness checklist, pressure-test scenarios, known historical bug classes. 

## 3. Test Harness Assumptions

The test harness should support:

* mocked ERC20 USDC with 6 decimals
* mocked ARM token with 18 decimals
* timestamp control for pre-deadline / post-deadline / expiry / 3-year claim deadline
* role impersonation for:

  * deployer
  * treasury
  * launch team / ROOT authority
  * Security Council
  * arbitrary participants

The harness should also include helpers for:

* creating seed graphs
* creating direct invites
* creating EIP-712 invite links
* generating EIP-1271-valid signatures
* forcing oversubscription at specific hops
* building repeated same-address and same-hop slot structures

## 4. Global Invariants

These must be tested both in deterministic scenarios and with fuzzing.

### 4.1 USDC conservation

For any finalized success-path outcome:

`net_proceeds + sum(refunds) == sum(total_deposited)`

For refundMode or cancel:

* no treasury transfer occurs
* participants can recover full refundable USDC per spec path. 

### 4.2 ARM conservation

At all times after settlement:

`allocated_arm + unsold_arm == MAX_SALE`

Where `MAX_SALE = 1,800,000 ARM` preloaded. 

### 4.3 Settlement invariant

For each address on the success path:

`sum(AllocatedHop.armAmount) == Allocated.totalArmAmount`

Also:

* `AllocatedHop` is only emitted when `armAmount > 0`
* no `AllocatedHop` exists for zero-allocation `(address, hop)` pairs.  

### 4.4 RefundMode event invariant

If `refundMode == true`:

* `Finalized(refundMode=true)` is emitted
* **no** `Allocated`
* **no** `AllocatedHop`
* `claim()` reverts
* `claimRefund()` returns full deposits.  

### 4.5 Cancel invariant

After `cancel()`:

* `commit()` reverts
* `finalize()` reverts
* `claimRefund()` is available for full deposits
* `withdrawUnallocatedArm()` can sweep all preloaded ARM
* state is irreversible. 

### 4.6 Slot / cap invariant

Participation is slot-based:

* hop-0 slots come from `SeedAdded`
* hop-1 and hop-2 slots come from `Invited`
* each slot increases cap by `HOP_CAP[hop]`
* outgoing invite rights scale with slots held at the inviter’s hop.  

### 4.7 Nonce invariant

* `nonce == 0` is reserved for direct invites only
* `commitWithInvite()` requires `nonce > 0`
* `revokeInviteNonce(0)` reverts
* each nonzero nonce can be consumed or revoked at most once. 

### 4.8 Lazy settlement invariants

* `finalize()` writes zero per-participant storage — only aggregate state (`saleSize`, `ceilings[]`, `hopDemand[]`, `totalAllocatedArm`, `totalArmTransferred = 0`)
* `computeAllocation(address)` is a pure view function — calling it does not modify state
* `computeAllocation(address)` returns identical results regardless of other participants' claim status (order-independence)
* `claim()` transfers ARM amount equal to `computeAllocation(msg.sender).armAmount` (within 3-year window) and refund equal to `computeAllocation(msg.sender).refundUsdc`
* `totalArmTransferred` increments only when ARM is actually sent (not when entitlement is computed)
* After 3-year expiry: `claim()` transfers refund USDC only, `armTransferred = 0`, `totalArmTransferred` does not increment

### 4.9 Commitment amount invariant

* `commit()` requires `amount > 0`
* `commitWithInvite()` requires `amount > 0`
* Zero-amount commits revert — no free slot acquisition without capital.

### 4.10 Seed uniqueness invariant

* `addSeed(address)` reverts if the address is already a seed
* Each address can hold at most one hop-0 slot
* Duplicate seeding is not a path to doubled hop-0 cap or invite rights.

### 4.11 Claim event ordering invariant

For each `claim()` call:

* `Allocated` is emitted with actual `armTransferred` (0 after expiry) and `refundUsdc`
* `AllocatedHop` events follow, one per hop with `acceptedUsdc > 0`
* `Allocated.armTransferred` equals the ARM actually transferred in this transaction
* Within the 3-year window: `sum(AllocatedHop.acceptedUsdc) * ARM_PER_USDC_RATE` (floored) equals `Allocated.armTransferred`
* After 3-year expiry: `Allocated.armTransferred == 0` while `AllocatedHop.acceptedUsdc` values remain unchanged (theoretical)

### 4.12 computeAllocation / claim equivalence invariant

For any participant, within the 3-year window:

* `computeAllocation(address).armAmount == Allocated.armTransferred` (from their `claim()` call)
* `computeAllocation(address).refundUsdc == Allocated.refundUsdc` (from their `claim()` call)
* `computeAllocation()` returns the same values before and after other participants claim (order-independence)
* only event timing differs, not economics.

## 5. Per-Function Test Matrix

Each external function must have:

* happy-path tests
* revert tests
* event tests
* state-change tests
* asset-movement tests

### 5.1 `loadArm()`

Test:

* succeeds exactly once with full preload amount
* reverts if called twice
* reverts if ARM balance/preload is insufficient
* emits `ArmLoaded`
* enables invite/commit window behavior only after successful call. 

### 5.2 `addSeed(seed)`

Test:

* callable only by authorized launch-team/ROOT authority
* only during allowed week-1 window
* only after ARM loaded
* creates hop-0 participation slot
* emits `SeedAdded`
* increments seed count
* enforces seed cap (150)
* **reverts if address is already a seed** — no duplicate hop-0 slots. 

### 5.3 `launchTeamInvite(invitee, fromHop)`

Test:

* callable only by authorized launch-team/ROOT authority
* only during week 1
* only after ARM loaded
* only for `fromHop = 0` (invitee joins hop-1) or `fromHop = 1` (invitee joins hop-2)
* **reverts for `fromHop >= 2`** — no hop-3+ creation
* emits `Invited(..., hop = fromHop + 1, nonce = 0)`
* creates participation slot for invitee
* decrements launch-team budget for the target hop separately (60 hop-1, 60 hop-2)
* verify both budget paths independently exhaust at 60. 

### 5.4 `invite(invitee, fromHop)`

Test:

* requires ARM loaded
* requires pre-deadline
* requires not cancelled
* requires not finalized
* requires inviter has available slots at `fromHop`
* creates invite edge and new participation slot at `fromHop + 1`
* emits `Invited(..., nonce = 0)`
* decrements inviter slot count
* permits duplicate same-hop invites to same address
* duplicate same-hop invites affect graph/slots/cap exactly once per invite. 

### 5.5 `commitWithInvite(inviter, fromHop, nonce, deadline, signature, amount)`

Test:

* signature valid for EOA via EIP-712
* signature valid for contract wallet via EIP-1271
* reverts on expired deadline
* reverts on used nonce
* reverts on revoked nonce
* reverts on `nonce == 0`
* **reverts on `amount == 0`** — no free slot acquisition
* reverts if inviter lacks available slot
* requires ARM loaded, pre-deadline, not cancelled, not finalized
* atomically emits `Invited(..., nonce)` and `Committed(...)`
* decrements inviter slots
* consumes nonce exactly once
* creates slot and cap at `fromHop + 1`
* raw deposit may exceed cap and remains refundable later. 

### 5.6 `revokeInviteNonce(nonce)`

Test:

* reverts on `nonce == 0`
* reverts if already consumed
* reverts if already revoked
* marks nonce revoked permanently
* emits `InviteNonceRevoked`. 

### 5.7 `commit(hop, amount)`

Test:

* requires participation slot at that hop
* hop-0 slot source is `SeedAdded`
* hop-1/hop-2 slot source is `Invited`
* requires ARM loaded, pre-deadline, not cancelled, not finalized
* **requires `amount > 0`** — zero-amount commits revert
* transfers USDC into escrow
* emits `Committed`
* allows over-cap deposits
* raw deposit can exceed effective capped amount without reverting. 

### 5.8 `finalize()`

Test success path:

* only post-deadline
* only if not finalized
* only if not cancelled
* only if `capped_demand >= MINIMUM_RAISE`
* writes aggregate state only: `saleSize`, `ceilings[]`, `hopDemand[]`, `totalAllocatedArm`, `totalArmTransferred = 0`
* zero per-participant storage writes
* sets `finalized = true`
* transfers net proceeds to treasury (`netProceeds = totalAllocatedUsdc`, USDC domain)
* emits `Finalized(saleSize, totalAllocatedArm, netProceeds, refundMode=false)`
* does NOT emit `Allocated` or `AllocatedHop` — those are emitted at `claim()` time

Test refundMode branch:

* if post-allocation `totalAllocatedUsdc < MINIMUM_RAISE`, sets `finalized = true` and `refundMode = true`
* emits `Finalized(refundMode=true)`
* records no aggregate allocation state beyond flags
* transfers no treasury proceeds
* emits no `Allocated` or `AllocatedHop` events

### 5.9 `computeAllocation(address)` (view function)

Test:

* callable after successful non-refundMode finalization
* returns `(armAmount, refundUsdc)` for any address
* returns `(0, 0)` for addresses that never committed
* result is deterministic — same output on repeated calls
* result is order-independent — unaffected by other participants' claim status
* matches the allocation algorithm: `acceptedUsdc` computed per hop from `ceilings[]` and `hopDemand[]`, then `armAmount = totalAcceptedUsdc * ARM_PER_USDC_RATE` (floor), `refundUsdc = totalCommittedUsdc - totalAcceptedUsdc`
* uses effective post-waterfall `ceilings[]` stored by `finalize()`, not raw BPS values
* correctly handles multi-slot participants: `effectiveUsdc = min(committed, slotCount * HOP_CAP)`

### 5.10 `claimRefund()`

Test eligibility paths:

1. `refundMode == true`
2. post-deadline, not finalized, `capped_demand < MINIMUM_RAISE`
3. cancelled

Test:

* refund amount equals full deposit in refundMode/cancel/pre-finalization-min-fail cases
* multiple claims revert (already claimed)
* emits `RefundClaimed`
* NOT callable on success path — success-path refunds go through `claim()`. 

### 5.11 `claim(delegate)`

Test:

* only after successful finalization (`finalized == true && refundMode == false`)
* computes allocation on-the-fly via `computeAllocation(msg.sender)` — result matches the view function
* sets `claimed[msg.sender] = true`
* within 3-year window: transfers ARM, calls `delegateOnBehalf(msg.sender, delegate)`, increments `totalArmTransferred`
* always transfers refund USDC if any (no expiry on refund portion)
* emits `Allocated(participant, armTransferred, refundUsdc, delegate)` — `armTransferred` is actual ARM sent
* emits `AllocatedHop` per hop with `acceptedUsdc > 0` (theoretical USDC domain, not affected by expiry)
* `nonReentrant` — verify reentrancy reverts
* multiple claims revert (already claimed)

Test post-expiry behavior (after 3-year deadline):

* `claim()` still callable — does NOT revert
* `claimed[msg.sender]` set to `true`
* ARM is NOT transferred (`armTransferred = 0`)
* `delegateOnBehalf` is NOT called
* `totalArmTransferred` does NOT increment
* refund USDC IS transferred (no expiry)
* emits `Allocated` with `armTransferred = 0`, `delegate = address(0)`
* participant permanently forfeits ARM entitlement. 

### 5.12 `cancel()`

Test:

* callable only pre-finalization by Security Council
* sets permanent cancelled state
* emits `Cancelled`
* disables new commits and finalization
* enables full refund claims and full ARM sweep. 

### 5.13 `withdrawUnallocatedArm()`

Test windows:

* immediate post-finalization unsold ARM sweep
* post-3-year deadline sweep of remaining ARM balance
* post-cancel sweep of full preloaded ARM

Test that each window only sweeps amounts authorized in that state.

Additional:

* **idempotency:** second call after a successful sweep transfers zero (or reverts) — no double-sweep
* **3-year boundary:** test exact timestamp at claim deadline — verify `claim()` reverts at deadline+1 and sweep becomes available. 

## 6. Deterministic Scenario Tests

These should be named fixtures.

### S1. Fully subscribed base sale

All hops oversubscribed below expansion trigger.
Verify:

* base sale size
* correct hop-0 / hop-1 ceilings
* hop-2 floor + leftover behavior
* pro-rata outputs
* both conservation invariants

### S2. Expansion threshold exact hit

`capped_demand == 1,500,000` exactly.
Verify:

* `sale_size = MAX_SALE`
* no off-by-one at threshold. 

### S3. Hop-0 only, base-size qualifying demand but insufficient net proceeds

Example: raw/capped demand between $1.0M and $1.5M all at hop-0.
Verify:

* finalization allowed to run
* post-allocation `net_proceeds < MINIMUM_RAISE`
* refundMode entered
* full refunds only
* no allocations recorded. 

### S4. Hop-0 only, expansion success

All demand at hop-0, `capped_demand >= 1.5M`.
Verify:

* expansion occurs
* hop-0 ceiling rises to expanded value
* success path can finalize. 

### S5. Hop-0 empty, all demand in hop-1

Verify:

* hop-1 effective ceiling can absorb remaining available
* no over-allocation
* rollover behavior correct. 

### S6. Zero hop-2 demand

Verify:

* floor reserved
* no hop-2 allocation
* reserved capacity contributes to unsold ARM, not phantom USDC. 

### S7. Single address at hops 0, 1, 2

Verify:

* allocations accumulate across all three hops
* no lowest-hop deduplication
* address receives multi-hop settlement
* per-hop settlement sums to aggregate. 

### S8. Recursive self-fill subtree

Seed self-invites and recursively fills downstream slots.
Verify:

* slot counts scale correctly
* caps scale correctly
* graph-legible structure translates to correct economic rights
* expected max-entity exposure is reachable only via the documented slot pattern. 

### S9. Duplicate same-hop invites

Same address receives multiple invites to same hop.
Verify:

* multiple slots created
* cap scales with slot count
* outgoing invite rights scale when appropriate
* duplicate edges do not corrupt settlement or display assumptions. 

### S10. Cancel before finalization

Verify:

* cancel path works from live window
* no commits/finalize afterward
* full refunds available
* all ARM sweepable. 

### S11. Lazy settlement — aggregate finalization

Verify:

* `finalize()` writes only aggregate state (`saleSize`, `ceilings[]`, `hopDemand[]`, `totalAllocatedArm`, `totalArmTransferred = 0`)
* `finalize()` emits only `Finalized` — no `Allocated` or `AllocatedHop` events
* zero per-participant storage writes during `finalize()`
* `computeAllocation(address)` returns correct values immediately after `finalize()`
* `claim(delegate)` computes and transfers correct amounts matching `computeAllocation()` output

### S11a. Lazy settlement — claim order independence

Multiple participants claim in different orders across two test runs with identical commitments.
Verify:

* each participant receives identical ARM and refund regardless of claim order
* `computeAllocation()` returns identical values before, during, and after other participants' claims
* `totalArmTransferred` after all claims equals `totalAllocatedArm` (within rounding)

### S11b. Lazy settlement — post-expiry claim

Participant calls `claim()` after 3-year deadline.
Verify:

* `claim()` does NOT revert
* `claimed[msg.sender] = true`
* refund USDC transferred
* ARM NOT transferred, `totalArmTransferred` NOT incremented
* `delegateOnBehalf` NOT called
* `Allocated` emitted with `armTransferred = 0`, `delegate = address(0)`
* participant cannot call `claim()` again (already claimed)

### S12. No-allocation participant after success

Address commits but receives zero ARM allocation due to full oversubscription at their hop(s).
Verify:

* `computeAllocation(participant)` returns `(0, fullRefundAmount)`
* `claim(delegate)` transfers only refund USDC (no ARM)
* `Allocated(participant, 0, fullRefundAmount, address(0))` emitted — zero ARM, full refund
* **no** `AllocatedHop` events for that participant (`acceptedUsdc = 0` at all hops → no per-hop events)
* `totalArmTransferred` does not increment

### S13. Over-issued invite links

Inviter creates 5 links for 3 available slots. First 3 redeemed succeed.
Verify:

* 4th and 5th redemptions revert with "inviter lacks available slot"
* first 3 slots correctly created
* nonces 4 and 5 remain unconsumed (not consumed-and-failed).

### S14. Zero-amount commit attempts

Test `commit(hop, 0)` and `commitWithInvite(..., amount=0)`.
Verify:

* both revert
* no slot created via zero-amount `commitWithInvite`
* no graph noise from zero-value commits.

### S15. Duplicate seed attempt

`addSeed(alice)` succeeds. `addSeed(alice)` again.
Verify:

* second call reverts
* alice has exactly 1 hop-0 slot, $15k cap, 3 outgoing invites
* seed count incremented only once.

### S16. Maximum network gas fixture

150 seeds, each with full subtrees (1,740 nodes total).
Verify:

* `finalize()` gas usage measured — should be 3-5M (aggregate-only: iterate participants for `hopDemand[]` + write aggregate state + treasury transfer)
* if gas exceeds 25M, investigate storage layout and participant enumeration implementation
* `claim()` gas measured per participant — should be ~200k
* `computeAllocation()` gas measured — should be negligible (view function)
* conservation invariants hold at scale

### S17. withdrawUnallocatedArm time-conditional sweep

Verify three sweep windows:

* **Immediately post-finalization (within 3yr):** sweeps `1,800,000 - totalAllocatedArm` (unsold only); does NOT touch allocated-but-unclaimed ARM
* **After 3-year deadline:** sweeps `1,800,000 - totalArmTransferred` (unsold + unclaimed + forfeited); sweep amount is larger than pre-expiry sweep
* **After cancel:** sweeps all 1,800,000 ARM

Test that the time check correctly determines which formula applies.

### S18. Claim event ordering

Multiple participants call `claim()`.
Verify:

* for each `claim()` call, `Allocated` log index < all of that participant's `AllocatedHop` log indices within the same transaction
* `Allocated.armTransferred` matches actual ARM transfer amount in the same transaction

### S19. Timestamp boundaries

Test exact-second behavior at:

* week-1 invite window close (day 7 → day 8)
* commitment deadline (day 21)
* invite link expiry (deadline field)
* 3-year claim deadline

Verify correct revert/accept at boundary and boundary ± 1 second.

## 7. Property / Fuzz Tests

Run fuzzing over:

* number of seeds
* invite graph shape
* duplicate invite patterns
* same-address multi-hop occupation
* same-address same-hop slot multiplication
* over-cap deposits
* demand distributions by hop
* phased batch sizes
* claim order
* refund claim order
* nonce creation / revocation / redemption order
* **timestamp proximity to boundaries** (week-1 close, commitment deadline, link expiry, 3-year claim deadline)
* **post-expiry claims** (varying when participants claim relative to the 3-year deadline)

Fuzz invariants:

* conservation laws (`totalArmTransferred + sweepableArm == 1,800,000`; `sum(refundUsdc) + netProceeds == totalCommitted` at convergence)
* `computeAllocation()` / `claim()` equivalence: for each participant, view output matches actual claim output
* order-independence: `computeAllocation()` returns identical values regardless of other claims
* no negative refunds / allocations
* no over-consumption of slots
* no duplicate nonce consumption
* no successful invite redemption after revoke or expiry
* no event emission forbidden by state
* no zero-amount commit succeeds
* no duplicate seed succeeds

## 8. Event Correctness Tests

Verify exact emitter / fields / absence conditions for:

* `ArmLoaded`
* `SeedAdded`
* `Invited`
* `Committed`
* `InviteNonceRevoked`
* `Finalized`
* `Allocated` (emitted at `claim()` time, includes `armTransferred`, `refundUsdc`, `delegate`)
* `AllocatedHop` (emitted at `claim()` time, includes `acceptedUsdc` in USDC domain)
* `RefundClaimed`
* `Cancelled`  

Specific checks:

* direct invites emit `Invited(..., nonce = 0)`
* link invites emit `Invited(..., nonce > 0)`
* `AllocatedHop` emitted only when `acceptedUsdc > 0`
* no `Allocated` or `AllocatedHop` events emitted by `finalize()` — only by `claim()`
* no `Allocated` or `AllocatedHop` events in refundMode or cancelled state
* **claim event ordering:** for each `claim()` call, `Allocated` log index < all of that participant's `AllocatedHop` log indices within the same transaction
* `Allocated.armTransferred` matches actual ARM transferred (0 after 3-year expiry)
* `commitWithInvite()` emits `Invited` and `Committed` in the same transaction — verify both present with correct fields

## 9. UI-Contract Compatibility Tests

These are not browser tests. They are contract-surface tests to protect the UI assumptions.

### Observer compatibility

Contract events must be sufficient for the observer to reconstruct:

* slot-based node graph from `SeedAdded` + `Invited`
* per-hop commitments from `Committed`
* claimed settlement from `Allocated` (emitted at `claim()` time)
* per-hop acceptance from `AllocatedHop` (emitted at `claim()` time)
* refundMode and cancel states from `Finalized` / `Cancelled`

Pre-claim theoretical allocations require the `computeAllocation(address)` view function — events alone are insufficient for pre-claim allocation display.

### Committer compatibility

Contract events and state must be sufficient for the committer to determine:

* connected address eligibility from `SeedAdded` and `Invited`
* link lifecycle from `Invited.nonce` + `InviteNonceRevoked`
* theoretical allocation via `computeAllocation(connectedAddress)` immediately after `Finalized`
* claim tab states:

  * `Finalized` received → show allocation from `computeAllocation()`, enable `claim(delegate)` button
  * `Allocated` received for this address → show confirmed settlement (actual ARM transferred + refund)
  * `Finalized(refundMode=true)` → show refund-only via `claimRefund()`

## 10. Exit Criteria

Implementation is ready for audit only when:

* all per-function happy-path tests pass (including `computeAllocation()` view function)
* all revert tests pass
* all deterministic scenarios pass (S1–S19, including S11a order-independence and S11b post-expiry)
* all global invariants pass under fuzzing (4.1–4.12, including lazy settlement invariants 4.8 and 4.12)
* `computeAllocation()` / `claim()` equivalence confirmed under fuzz (view output matches actual claim output for all participants)
* all event-surface tests pass including claim-time ordering guarantees
* observer and committer compatibility tests pass (including `computeAllocation()` view function)
* `finalize()` gas benchmark completed at maximum network scale (S16)
* slot-count model confirmed: `slotCount[address][hop]` is a counter, not a boolean
* every major mechanism rule in `CROWDFUND.md` maps to at least one explicit test in `SPEC_TRACEABILITY.md`

---

## What should be produced from this

* `Crowdfund.t.sol` — per-function unit tests (§5)
* `CrowdfundScenarios.t.sol` — deterministic scenario fixtures (§6, S1–S19)
* `CrowdfundInvariants.t.sol` — invariant / fuzz harness (§4, §7)
* `CrowdfundEvents.t.sol` — event correctness and ordering tests (§8)
* `CrowdfundUiSurface.t.sol` — observer / committer compatibility tests (§9)
* `CrowdfundGas.t.sol` — gas measurement fixtures (S16: max-network finalization)
* `SPEC_TRACEABILITY.md` — mapping from spec rules to tests; every rule in `CROWDFUND.md` must appear

