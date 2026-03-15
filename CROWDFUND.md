# Armada Crowdfunding Spec
⚠️ **in flux** ⚠️

This is a draft, open for commentary [Discord](https://discord.gg/KGN3QQRYAt)

## Overview

Armada will raise funds by "word-of-mouth whitelisting." The mechanism is algorithmic, predeclared, and non-discretionary.

### Round 1

| Parameter | Value |
|---|---|
| Purpose | Fund MVP |
| Min raise | 1,000,000 ARM ($1.0M) |
| Base raise | 1,200,000 ARM ($1.2M) |
| Max raise (elastic) | 1,800,000 ARM ($1.8M) |
| Expansion trigger | $1,500,000 in capped demand |
| Percent of supply | 10 - 15% |
| Price | $1.00 per ARM |
| Timing | Pre-product |

### Round 2 (Not Guaranteed)

Round 2 depends on MVP progress, market conditions, and governance decisions. Round 1 participants are acquiring ARM tokens in a pre-product protocol with governance power and no promise of future crowdfunding or liquidity events.

If Round 2 occurs:

| Parameter | Value |
|---|---|
| Purpose | Fund v1 |
| Raise | 1,200,000 ARM ($1.5M) |
| Supply | 10% |
| Price | $1.25 per ARM |
| Timing | MVP launch |

---

## Token Distribution ⚠️ in flux ⚠️

**Total supply: 12,000,000 ARM**

| Allocation | ARM | % | Unlock | Voting Power |
|---|---|---|---|---|
| Round 1 crowdfund | 1,200,000 - 1,800,000 | 10-15% | Governance proposal | Immediate |
| Round 2 crowdfund | 1,200,000 | 10% | Governance proposal | Immediate |
| Team | 1,200,000 | 10% | Revenue milestones | Revenue-gated |
| Airdrop | 600,000 | 5% | Revenue milestones | Revenue-gated |
| Treasury | 7,200,000 - 7,800,000 | 60-65% | Governance-controlled | None |

### Pre-Crowdfund Distribution

Before Round 1 opens, the following revenue-locked allocations are distributed and published:

**Team Allocation (1,200,000 ARM):**
- Distributed to founding team multisig
- Multisig assigns to individual team members
- All assignments are revenue-locked
- Distribution is published before crowdfund

**Airdrop Allocation (600,000 ARM):**
- Distributed to qualifying recipients
- Revenue-locked (same terms as team)
- Criteria and recipient list TBD

**Transparency:**

Crowdfund participants can evaluate the full distribution before committing: who holds what and under what terms.

### Team Allocation

The team allocation (1,200,000 ARM, 10%) is controlled by a founding team multisig.

**Properties:**
- Revenue-locked transferability (same schedule as all team/airdrop tokens)
- Voting power unlocks proportionally with revenue milestones
- Portions within the multisig can be reassigned to individual team members
  - Internal splits do not require governance approval

**Constraints:**
- Cannot change the revenue-lock schedule
- Cannot pull from treasury or other allocations
- All reassigned portions inherit the same unlock terms

**Use cases:**
- Assigning tokens to new team members
- Adjusting allocations as roles change
- Recovering allocation if a team member departs

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

This is a trusted-network sale, not viral growth. Participation requires invitation from someone closer to the core. Each hop has a ceiling — the maximum fraction of the raise it can absorb. Hop-2 additionally has a hard floor: a minimum reservation off the top of the raise, guaranteeing governance breadth even when earlier hops are fully subscribed. Actual allocation is demand-driven: each hop receives whatever it committed, up to its ceiling, and anything unused rolls forward to later hops.

### Seed Selection

Seed criteria are determined by the founding team based on demonstrated alignment, contribution, and long-term interest in privacy infrastructure.

### Hop Structure

| Hop | Description | Ceiling | Floor | Cap | Invites |
|---|---|---|---|---|---|
| 0 | Seeds | 70% | — | $15,000 | 3 |
| 1 | Seed invites | 45% | — | $4,000 | 2 |
| 2 | Hop-1 invites | 10% | 5% | $1,000 | 0 |

Hop-2 gets first claim on 5% of the raise before hop-0 and hop-1 ceilings are applied. This guarantees governance breadth even under strong demand from earlier hops. Hop-0 and hop-1 ceilings are calculated against the raise net of the hop-2 floor reservation. At base raise: $60k reserved for hop-2, hop-0 and hop-1 compete for the remaining $1.14M.

Ceilings are overlapping by design — they sum to 125%, not 100%. No hop is guaranteed its ceiling above the floor. Each hop absorbs up to its ceiling based on actual demand; anything unused rolls forward. If all hops are fully subscribed, earlier hops fill first and later hops absorb whatever remains up to their ceiling.

### Ceiling Amounts

Hop-2 floor reservation comes off the top. Hop-0 and hop-1 ceilings are applied against the remainder.

**Base raise ($1.2M):**

| Hop | Floor reservation | Available pool | Ceiling | Max allocation |
|---|---|---|---|---|
| 2 | $60,000 | — | 10% of $1.14M | $114,000 (≥ $60k floor) |
| 0 | — | $1,140,000 | 70% | $798,000 |
| 1 | — | $1,140,000 | 45% | $513,000 |

**Expanded raise ($1.8M):**

| Hop | Floor reservation | Available pool | Ceiling | Max allocation |
|---|---|---|---|---|
| 2 | $90,000 | — | 10% of $1.71M | $171,000 (≥ $90k floor) |
| 0 | — | $1,710,000 | 70% | $1,197,000 |
| 1 | — | $1,710,000 | 45% | $769,500 |

### Invitation Limits

Invitation limits are hop-specific and predeclared:

- Hop-0 → may invite up to 3 addresses to hop-1
- Hop-1 → may invite up to 2 addresses to hop-2
- Hop-2 → may not invite

Invitations are signed on-chain (inviter → invitee). An address can only participate at one hop level.

### Self-Filling and Max Single-Entity Capture

A hop-0 participant may invite their own addresses to hop-1 and hop-2. This is not a bug. Attempting to technically enforce unique addresses doesn't work on-chain — anyone determined to bypass it uses different wallets — and socially prescribing it creates a two-tier outcome: participants who follow the norm get less, those who don't get more. That's a bad equilibrium, especially under oversubscription where pro-rata amplifies the gap.

The mechanism is designed so that self-filling is transparently possible but economically bounded:

| Hop | Cap | Max addresses (self-filled) | Max USDC |
|-----|-----|-----------------------------|----------|
| Hop-0 | $15,000 | 1 | $15,000 |
| Hop-1 | $4,000 | 3 | $12,000 |
| Hop-2 | $1,000 | 6 | $6,000 |
| **Total** | | **10 addresses** | **$33,000** |

$33,000 out of a $1.2M raise is 2.75% — meaningful, but not structurally threatening. And it requires real capital deployed across 10 addresses. The actual deterrents:

1. **Real capital required at every hop.** Self-filling isn't free. $33k fully deployed is the maximum, not a rounding error.
2. **Cap differential.** Hop-0 gets $15k, hop-1 gets $4k. A seed who self-invites to access more hop-1 slots is trading $15k access for $4k access per address. The opportunity cost is concrete.
3. **Post-sale graph visibility.** Full invite graph is published after finalization. Chains of self-invites are visible.
4. **Rollover thresholds.** Rollover to later hops requires minimum unique committers, which limits how much leftover capacity a single entity can capture through sybil addresses.
5. **Governance concentration has low ROI pre-revenue.** Accumulating votes in a protocol with no product yet is not obviously valuable.

Residual risk—a seed concentrating votes at scale—is primarily mitigated by trusted-network seed selection. The mechanism does not make it impossible; it makes it expensive, visible, and bounded.

---

## Sale Mechanics

### Timeline

| Phase | Duration |
|---|---|
| Unified window | 3 weeks |

Invites and commitments are open simultaneously from day 1. There is no sequential dependency — a newly-invited participant can commit immediately. Both close at the same hard deadline.

### Commitment

- **Currency:** USDC
- **Method:** Commit to escrow contract
- **Visibility:** Address, amount, hop are public; invite edges hidden until after sale

Real-time aggregate statistics show current commitment levels per hop throughout the window, allowing participants to observe oversubscription before committing.

### Finalization

After the unified window closes, the contract finalizes in a single admin-triggered transaction:

1. Sale size is determined (base or expanded, based on total capped demand)
2. Per-hop ceilings are calculated
3. Allocations are computed (pro-rata where oversubscribed)
4. Refunds are computed
5. **USDC proceeds transfer immediately to treasury** — no participant action required
6. Refund USDC returns immediately to participant addresses
7. ARM allocations are recorded on-chain for participants to claim

ARM claim remains a separate participant-initiated step (one transaction per claimant). USDC flows to treasury at finalization regardless of whether participants have claimed their ARM.

**Unclaimed ARM is vote-inert.** Allocated-but-unclaimed ARM does not count toward quorum or carry voting power. ARM becomes active — and transferable once governance unlocks transfers — only after the participant submits their claim transaction.

**Claim deadline: 3 years from finalization.** Unclaimed ARM after this deadline is recoverable by the treasury via governance proposal. The deadline is fixed and cannot be extended — it is a predeclared term of participation, not a governance parameter.

Post-finalization, the crowdfund admin role transfers to the governance timelock via a two-step pattern. Only `withdrawUnallocatedArm()` remains relevant post-finalization; all other admin functions are inactive.

### Allocation

Each hop has a ceiling — the maximum it can absorb from the total raise. Allocation is demand-driven: each hop receives `min(actual demand, ceiling)`. Anything below the ceiling is leftover and rolls forward. Oversubscription (demand exceeding ceiling) is resolved pro-rata, bounded by per-address caps.

```python
for hop in [0, 1, 2]:
    ceiling = hop_ceilings[hop]  # ceiling × sale_size
    effective = {a: min(a.committed, hop_cap[hop]) for a in hop_addresses}
    total_demand = sum(effective.values())
    
    if total_demand <= ceiling:
        for a in hop_addresses:
            a.allocation = effective[a]
        leftover[hop] = ceiling - total_demand
    else:
        scale = ceiling / total_demand
        for a in hop_addresses:
            a.allocation = effective[a] * scale
        leftover[hop] = 0
```

### Rollover Rules

Unused ceiling capacity rolls down to later hops, subject to participation thresholds:

| Condition | Action |
|---|---|
| Hop-1 unique committers ≥ 30 | Hop-0 leftover → hop-1 (increases hop-1 effective ceiling) |
| Hop-1 unique committers < 30 | Hop-0 leftover → treasury |
| Hop-2 unique committers ≥ 50 | Hop-1 leftover → hop-2 (increases hop-2 effective ceiling) |
| Hop-2 unique committers < 50 | Hop-1 leftover → treasury |

Leftover never rolls back to earlier hops. This prevents seeds from benefiting from failed propagation.

When leftover rolls forward, the receiving hop's effective ceiling increases by the leftover amount. The hop then allocates against this expanded ceiling using the same demand-driven logic. Total allocation across all hops cannot exceed the sale size.

### Elastic Expansion (Round 1 Only)

Round 1 may expand if demand significantly exceeds base supply. Round 2 (if occurs) is fixed.

```python
if total_capped_demand >= 1_500_000:  # $1.5M absolute threshold
    sale_supply = MAX_SALE  # 1,800,000 ARM ($1.8M, 15% of supply)
else:
    sale_supply = BASE_SALE  # 1,200,000 ARM ($1.2M, 10% of supply)
```

Expansion is binary: either base ($1.2M) or max ($1.8M). If capped demand clears $1.5M, supply expands to maximum; excess demand beyond $1.8M is refunded pro-rata.

### Refunds

```
refund_usdc = committed_usdc - (allocation_arm × price)
```

Refunds are automatic and immediate after allocation finalizes.

---

## Round 2 Whitelist Access

If Round 2 occurs, Round 1 participants receive automatic hop-0 status:

- Same cap as other hop-0 seeds
- Can invite up to 3 addresses to hop-1
- Compete for hop-0 ceiling alongside new seeds

Additional hop-0 seeds may be added based on MVP-phase relationships.

Round 1 participation is rewarded via access, not superlinear allocation.

---

## Governance

### Voting Power

Voting power depends on token type and protocol revenue:

| Token Type | Voting Power |
|---|---|
| Crowdfund (Round 1 + Round 2) | Full, immediate |
| Team | Proportional to revenue-based unlock |
| Airdrop | Proportional to revenue-based unlock |
| Treasury | None |

**Example at $0 revenue:**
- Only crowdfund tokens vote
- Team and airdrop have no governance power
- All decisions made by people who paid

**Example at $100k cumulative revenue:**
- Crowdfund: 1.5M ARM voting (100%)
- Team: 480k ARM voting (40% of 1.2M)
- Airdrop: 240k ARM voting (40% of 600k)
- Treasury: 0 ARM voting

### Rationale

Governance power follows economic commitment. Crowdfund participants paid for their tokens and bear the risk of failure. Team and airdrop recipients earn governance influence by generating protocol revenue—the same milestone that unlocks their tokens.

This removes ambiguity: only people who paid decide unlock timing, treasury strategy, and protocol direction until the protocol proves itself.

### Commitment Requirement

ARM must be committed to vote. Uncommitted ARM does not count toward quorum or voting.

Voting power can be delegated to any address.

### Quorum and Passing

```
quorum = 20% of committed voting-eligible ARM
passing = >50% of votes cast
```

**Example at Round 1 close (no revenue):**
- 1.2M ARM voting-eligible (crowdfund only)
- If 800k ARM committed: 160k quorum, 80k+ to pass

**Example after $250k revenue:**
- 1.2M crowdfund + 720k team + 360k airdrop = 2.28M voting-eligible
- If 1.5M committed: 300k quorum, 150k+ to pass

### Proposal Timeline

| Phase | Duration |
|---|---|
| Voting delay | 2 days |
| Voting period | 7 days |
| Enactment delay | 7 days |
| **Total** | **16 days** |

### Token Unlock Proposal

Crowdfund tokens become transferable via governance proposal. There are no predeclared conditions — holders decide when. ARM must be claimed before it can be transferred; unclaimed allocations are inert regardless of any unlock proposal that passes.

Team and airdrop tokens cannot be unlocked by governance. They unlock only via revenue milestones.

### Treasury Control

Treasury tokens (60-65%) are controlled by governance but do not vote.

### Treasury Distributions

**USDC Operations (Steward):**
- One-time payments up to $40k: steward proposal, pass by default, 5-day veto window
- Steward submits monthly operations proposals as batched payments
- No recurring or streaming payments in v1

**ARM Distributions (Governance Only):**
- Require standard governance proposal
- Distributed ARM has full voting power immediately

### Steward Role ⚠️ in flux ⚠️

A steward can be elected with special powers for treasury fund operations.

**Scope:**
- USDC operations only (grants, expenses, service payments)
- Cannot propose ARM distributions
- Cannot modify protocol parameters
- Cannot vote on unlock proposals

**Limits:**
- Per-proposal maximum: $40,000 USDC
- Monthly maximum: $120,000 USDC
- Proposals exceeding limits require standard governance

**Process:** 
- Steward proposals pass by default unless voted down
- Veto window: 7 days
- Only voting-eligible ARM can veto

**Term:**
- 6 months, renewable by governance proposal
- Ejection by standard governance proposal (immediate effect)

---

## Invite Graph Visibility

| Phase | Visibility |
|---|---|
| During sale | Aggregates only (counts, totals by hop) |
| After finalization | Full graph (all edges, all hops) |

Post-sale transparency strengthens legitimacy. The privacy-sensitive moment is during commit, not after.

---

## Wind-Down

If integrator demand or volume does not materialize, governance may vote to wind down:

1. Shielded pool enters withdraw-only mode (users can always exit)
2. Treasury distributed to circulating ARM holders
3. Team and airdrop tokens have no treasury claim (zero-cost-basis exclusion)

Those who paid have priority over those who received tokens at zero cost basis.

Idea: there could be an automated wind-down, for example if 10k USDC in revenue has not been achieved in 8 months.

---

## Risk Acknowledgments

- **Seed quality is critical.** The mechanism scales trust outward from seeds.
- **Governance capture is possible.** Whitelist is the mitigation, not a guarantee.
- **Propagation may fail.** Unused supply flows to treasury.
- **Round 2 is not guaranteed.** Round 1 is a standalone commitment.
- **This is a trusted-network sale.** Not designed for viral distribution.
- **Non-transferability is intentional.** Prioritizes governance participation over liquidity during formative phase.

---

## Summary of Design Decisions

| Decision | Resolution | Rationale |
|---|---|---|
| Treasury voting | Treasury does not vote | Avoids control ambiguity |
| Team/airdrop voting | Revenue-gated | Only people who paid decide until protocol proves itself |
| Steward voting eligibility | Voting-eligible ARM only | Treasury decisions by economic stakeholders |
| Invite graph | Full reveal post-sale | Transparency strengthens legitimacy |
| Enactment delay | 7 days | Prevents coordination without feeling like lockup |
| Round 2 caps | Same as other hop-0 seeds | Rewards access, not superlinear allocation |
| Elastic expansion | Round 1 only, binary to 1.8M ARM | Accommodates demand without Round 2 price distortion |
| Team allocation control | Multisig reassignment | Flexibility without governance overhead |
| Pre-crowdfund distribution | Team + airdrop distributed before Round 1 | Full transparency for funders |
| Recurring payments | Not supported in v1 | Steward submits monthly batches instead |
| Hop allocation model | Demand-driven ceilings, not fixed reserves | Capacity follows actual demand; no hop is guaranteed allocation it didn't earn; unused capacity rolls forward |
| Overlapping ceilings | Sum to 125% (70/45/10) | Intentional — later hops can absorb more if earlier hops underperform, without needing to predeclare exact splits |
| No per-hop floors | Informational only | Global minimum raise ($1M) provides the real floor; per-hop floors add complexity without meaningful protection |
| Hop-2 hard floor | 5% of raise reserved off the top | Hop-2 is governance breadth and ecosystem signal — mechanism should back that up. Ensures hop-2 always gets something even under strong hop-0/hop-1 demand. Negligible impact on earlier hops (~$50 less per participant at full subscription) |
| Self-filling downstream hops | Permitted, disclosed | Technical enforcement doesn't work; social prescription penalises honest participants vs those who bypass it. Max capture is bounded ($33k / 10 addresses) and visible post-sale |
| Invite + commitment phases | Unified single window (3 weeks) | Sequential phases create coordination friction and disadvantage late invitees. Unified window: invites and commitments open simultaneously, both close at hard deadline. Network keeps growing while commitments are happening |
| 2-step commit/claim | Removed for USDC; ARM claim retained | Treasury should not be blocked waiting on participant action. USDC transfers to treasury at finalization automatically. ARM claim remains participant-initiated (one tx) — acceptable friction for token receipt vs unacceptable for treasury funding |
| Crowdfund admin post-finalization | Transfers to governance timelock (two-step) | Admin role (`addSeeds`, `finalize`, `cancel`, `withdrawProceeds`) is deployer-controlled during sale; post-finalization, only `withdrawUnallocatedArm` remains active and should be timelock-controlled |
| Unclaimed ARM voting power | Vote-inert until claimed | Allocated-but-unclaimed ARM should not affect quorum or carry governance weight. Prevents phantom voting power from lost keys or inactive participants |
| ARM claim deadline | 3 years from finalization, fixed | Predeclared term of participation, not a governance parameter. Treasury recovers abandoned allocations after lapse. Ungovernable by design — prevents future holders from extending it in self-interest |

---

## Appendix: Allocation Pseudocode

```python
# Constants
TOTAL_SUPPLY = 12_000_000
BASE_SALE = 1_200_000
MAX_SALE = 1_800_000
EXPANSION_TRIGGER = 1_500_000  # $1.5M absolute — triggers expansion from $1.2M to $1.8M

# Overlapping ceilings — sum to 125%, not 100%.
# Each hop absorbs up to its ceiling; unused rolls forward.
# Hop-2 has a hard floor: first claim on HOP2_FLOOR_PCT of raise,
# taken off the top before hop-0/hop-1 ceilings are calculated.
HOP_CEILING_PCT = {0: 0.70, 1: 0.45, 2: 0.10}
HOP2_FLOOR_PCT = 0.05  # hop-2 guaranteed minimum as % of raise
HOP_CAP = {0: 15_000, 1: 4_000, 2: 1_000}
PRICE = 1.00  # USDC per ARM

ROLLOVER_THRESHOLD = {1: 30, 2: 50}  # minimum unique committers

def allocate(commitments):
    # Step 1: Determine sale size (binary expansion)
    capped_demand = sum(
        min(c.usdc_committed, HOP_CAP[c.hop]) 
        for c in commitments
    )
    
    if capped_demand >= EXPANSION_TRIGGER:
        sale_size = MAX_SALE
    else:
        sale_size = BASE_SALE
    
    # Step 2: Calculate per-hop ceilings
    # Hop-2 floor is reserved first; hop-0/hop-1 ceilings apply to remainder.
    hop2_floor = HOP2_FLOOR_PCT * sale_size
    available = sale_size - hop2_floor  # pool for hop-0 and hop-1
    
    ceilings = {
        0: HOP_CEILING_PCT[0] * available,
        1: HOP_CEILING_PCT[1] * available,
        2: max(hop2_floor, HOP_CEILING_PCT[2] * available),  # floor or normal, whichever larger
    }
    effective_ceilings = dict(ceilings)  # updated during rollover
    
    # Step 3: Allocate per hop (demand-driven, up to ceiling)
    allocations = {}
    leftover = {}
    
    for hop in [0, 1, 2]:
        hop_commits = [c for c in commitments if c.hop == hop]
        effective = {c.address: min(c.usdc_committed, HOP_CAP[hop]) for c in hop_commits}
        demand = sum(effective.values())
        ceiling = effective_ceilings[hop]
        
        if demand <= ceiling:
            for addr, amt in effective.items():
                allocations[addr] = amt / PRICE
            leftover[hop] = ceiling - demand
        else:
            scale = ceiling / demand
            for addr, amt in effective.items():
                allocations[addr] = (amt * scale) / PRICE
            leftover[hop] = 0
    
    # Step 4: Rollover
    # Leftover increases the next hop's effective ceiling, then reallocates.
    def unique_committers(hop):
        return len(set(c.address for c in commitments if c.hop == hop))
    
    def reallocate_hop(hop, additional_capacity):
        hop_commits = [c for c in commitments if c.hop == hop]
        effective = {c.address: min(c.usdc_committed, HOP_CAP[hop]) for c in hop_commits}
        demand = sum(effective.values())
        new_ceiling = effective_ceilings[hop] + additional_capacity
        effective_ceilings[hop] = new_ceiling
        
        if demand <= new_ceiling:
            for addr, amt in effective.items():
                allocations[addr] = amt / PRICE
            return new_ceiling - demand
        else:
            scale = new_ceiling / demand
            for addr, amt in effective.items():
                allocations[addr] = (amt * scale) / PRICE
            return 0
    
    treasury_leftover = 0
    
    # Roll hop-0 leftover to hop-1
    if leftover[0] > 0:
        if unique_committers(1) >= ROLLOVER_THRESHOLD[1]:
            leftover[1] = reallocate_hop(1, leftover[0])
        else:
            treasury_leftover += leftover[0]
    
    # Roll hop-1 leftover to hop-2
    if leftover[1] > 0:
        if unique_committers(2) >= ROLLOVER_THRESHOLD[2]:
            remaining = reallocate_hop(2, leftover[1])
            treasury_leftover += remaining
        else:
            treasury_leftover += leftover[1]
    
    # Any hop-2 leftover goes to treasury
    treasury_leftover += leftover.get(2, 0)
    
    # Step 5: Calculate refunds
    refunds = {}
    for c in commitments:
        allocated_usdc = allocations.get(c.address, 0) * PRICE
        refunds[c.address] = c.usdc_committed - allocated_usdc
    
    return allocations, refunds, treasury_leftover
```
