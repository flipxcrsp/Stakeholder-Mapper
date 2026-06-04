# Stakeholder Mapper

> A B2B sales intelligence tool for mapping buying committees, visualizing stakeholder dynamics, and surfacing deal risk before it's too late.

## Live Demo

🔗 [Launch Stakeholder Mapper](https://stakeholder-mapper.base44.app)

---

## The Problem

Enterprise deals are won or lost in the buying committee — not in a single conversation. Most CRMs track *contacts*, but they don't help you understand the **relationships**, **influence**, and **sentiment** that determine whether a deal closes.

Sales teams often discover too late that:
- They never found the Economic Buyer
- A silent Blocker killed the deal internally
- Their Champion had no real organizational influence
- Relationship strength was concentrated in one person who left

**Stakeholder Mapper** gives revenue teams a structured way to map every player in a deal, score the health of their relationships, and get proactive signals when a deal is at risk.

---

## Key Features

### 🗂 Account Management
- Create and manage accounts with deal stage, deal value, and industry
- Dashboard view with real-time Deal Risk badges per account
- Search and filter accounts by stage (Prospecting → Closed Won/Lost)

### 🕸 Interactive Relationship Map
- Force-directed D3 network graph visualizing the entire buying committee
- Node size represents **influence score** (1–10)
- Node color represents **sentiment** (Supporter / Neutral / Blocker)
- Edge thickness represents **relationship strength** between stakeholders in the same department
- Draggable nodes, zoom/pan, and click-to-inspect
- Filter overlay to highlight Champions, Blockers, Decision Makers, Economic Buyers, Executive Sponsors, and Missing Types (ghost nodes)

### ⚠️ Deal Risk Engine
- Automated risk scoring based on committee composition and relationship health
- Inline risk panel overlaid on the relationship map
- Three risk levels: **Healthy**, **At Risk**, **Critical**

### 📊 Account Summary Dashboard
- Sentiment distribution chart (Recharts pie)
- Key stakeholder callouts: strongest Champion, top Decision Maker
- Coverage gap detection: lists missing buying committee roles
- AI-generated next-best engagement recommendations

### 👥 Team View
- Lists all workspace members with roles and account ownership counts

---

## User Roles

| Role | Capabilities |
|------|-------------|
| **Admin** | Full access to all accounts, stakeholders, and team management |
| **User (Account Owner)** | Creates and manages their own accounts; sees private notes on their stakeholders |
| **User (Team Member)** | Views shared accounts; cannot edit private notes or delete records they don't own |

> Ownership is tracked via `owner_id` on each Account. Private stakeholder notes are only visible to the account owner.

---

## Deal Risk Methodology

The Deal Risk engine evaluates each account's buying committee against four criteria:

| Factor | Severity | Trigger |
|--------|----------|---------|
| Missing Economic Buyer | 🔴 High | No stakeholder with type `Economic Buyer` exists |
| Missing Champion | 🔴 High | No stakeholder with type `Champion` exists |
| Blocker Concentration | 🟡 Medium | ≥ 33% of stakeholders have sentiment `Blocker` |
| Low Relationship Strength | 🟡 Medium | Average `relationship_strength` across all stakeholders < 4 / 10 |

**Risk Level is determined as:**
- **Critical** — any High severity factor is present
- **At Risk** — only Medium severity factors are present
- **Healthy** — no risk factors detected

This methodology is implemented in `components/DealRiskBadge.jsx` via the exported `computeDealRisk(stakeholders)` function, which is shared across the Account Card, Relationship Map overlay, and Account Summary panel.

---

## Relationship Map Methodology

The relationship map is built using a **D3 force-directed graph** (`d3-force`, `d3-selection`, `d3-drag`, `d3-zoom`).

### Nodes
Each stakeholder becomes a node. Node attributes map to visual dimensions:

| Stakeholder Attribute | Visual Representation |
|-----------------------|-----------------------|
| `influence_score` (1–10) | Node radius (larger = more influential) |
| `sentiment` | Node fill color (green / amber / red) |
| `type` | Unicode symbol inside the node (★ ◆ ▲ etc.) |

### Edges
Edges are drawn between stakeholders **in the same department**. Edge thickness encodes the minimum `relationship_strength` between the two connected nodes:

| Strength Range | Edge Weight |
|----------------|-------------|
| 1–3 (Weak) | 1.5px |
| 4–6 (Medium) | 3.5px |
| 7–10 (Strong) | 6px |

### Ghost Nodes
When the "Missing Types" filter is active, placeholder ghost nodes (dashed outline, `+` symbol) are rendered for any buying committee role not yet represented in the account — making coverage gaps visually obvious.

### Forces
The simulation uses:
- `forceManyBody` — repulsion between all nodes
- `forceLink` — attraction along department edges
- `forceCenter` — gravity toward canvas center
- `forceCollide` — prevents node overlap, radius-aware

---

## Technical Architecture

### Stack
| Layer | Technology |
|-------|-----------|
| Frontend | React 18 + Vite |
| Styling | Tailwind CSS + shadcn/ui |
| Data Visualization | D3 v7 (submodule imports) |
| Charts | Recharts |
| State / Data Fetching | TanStack React Query |
| Animations | Framer Motion |
| Backend / DB | Base44 (BaaS — entities, auth, integrations) |
| Auth | Base44 Auth (email/password + Google OAuth) |

### Data Model

**Account**
```
name, industry, deal_stage, deal_value, owner_id, owner_name
```

**Stakeholder**
```
account_id, name, title, department, email,
type (enum), influence_score, relationship_strength,
sentiment (enum), business_priorities, notes
```

### Project Structure
```
src/
├── pages/
│   ├── Accounts.jsx          # Dashboard / account list
│   ├── AccountDetail.jsx     # Per-account view (map, summary, list tabs)
│   └── Team.jsx              # Team member directory
├── components/
│   ├── RelationshipMap.jsx   # D3 force graph
│   ├── AccountSummary.jsx    # Deal health dashboard
│   ├── StakeholderSidePanel.jsx
│   ├── StakeholderForm.jsx
│   ├── AccountCard.jsx
│   ├── AccountForm.jsx
│   ├── DealRiskBadge.jsx     # Risk engine + badge
│   ├── StakeholderTypeBadge.jsx
│   └── Layout.jsx
├── entities/
│   ├── Account.json
│   └── Stakeholder.json
```

---

## Screenshots

> _Add screenshots of the Accounts dashboard, Relationship Map, and Account Summary here._

---

## Getting Started

This application is built and hosted on [Base44](https://base44.com). To run your own instance:

1. Fork the app on Base44
2. Invite your team members via the Team page
3. Create your first Account and add Stakeholders
4. Open the Relationship Map to visualize your buying committee

---

## License

MIT
