# Team Changes Across Project Phases
> A comprehensive reference guide for enterprise software project lifecycle

---

## 1. Phase Definitions

### Phase 0: Sales
> The phase where the **business development and pre-sales team engages the client**, understands their needs, proposes a solution, and secures the contract before any project work begins.

| | Details |
|---|---|
| **When** | Before project kick-off |
| **Duration** | Weeks to months (depends on deal complexity) |
| **Who** | Sales, Pre-sales, Solution Consultant, Business Development |
| **Goal** | Win the contract and hand over to delivery team |
| **Budget** | Sales/Business Development budget |

**What happens during Sales:**
- Understand client pain points and requirements at a high level
- Propose a solution and prepare proposal / RFP response
- Estimate project cost, timeline, and team size
- Negotiate contract terms
- Hand over signed contract and requirements summary to project team

---

### Phase 1: Design
> The phase where the project team **deeply analyzes requirements and designs the full solution** — architecture, database, UI/UX, and technical approach — before any coding begins.

| | Details |
|---|---|
| **When** | After project kick-off, before development |
| **Duration** | Weeks (depends on system complexity) |
| **Who** | BA, Solution Architect, Tech Lead, UI/UX Designer |
| **Goal** | Produce detailed design artifacts ready for development |
| **Budget** | CapEx (Capital Expenditure) |

**What happens during Design:**
- Detailed requirements gathering and analysis
- System architecture and infrastructure design
- Database schema design
- API contract definition
- UI/UX wireframes and prototypes
- Technical stack decisions
- Produce design documents for developer handover

---

### Phase 2: Development
> The phase where the full project team **builds the system from scratch**, following the full SDLC (Software Development Life Cycle).

| | Details |
|---|---|
| **When** | After design is approved |
| **Duration** | Weeks to months (depends on project size) |
| **Who** | Full project team |
| **Goal** | Deliver a working system ready for user testing |
| **Budget** | CapEx |

---

### Phase 3: UAT (User Acceptance Testing)
> The phase where **real users test the system** to validate it meets business requirements before go-live.

| | Details |
|---|---|
| **When** | After development is complete |
| **Duration** | Typically 2 to 6 weeks |
| **Who** | BA, QA, key developers, business users |
| **Goal** | Get sign-off from business that system is ready |
| **Budget** | CapEx |

---

### Phase 4: Go-Live
> The moment the system is **released to production** and real users start using it.

| | Details |
|---|---|
| **When** | After UAT sign-off |
| **Duration** | A single event (day) |
| **Who** | Full team on standby |
| **Goal** | Successfully deploy system to production |
| **Budget** | CapEx |

---

### Phase 5: Hypercare
> The **short-term intensive support period immediately after go-live**, where the original development team stays on standby to quickly catch and fix any critical issues before fully handing over to the maintenance team.

| | Details |
|---|---|
| **When** | Immediately after go-live |
| **Duration** | Typically 2 to 4 weeks |
| **Who** | Original dev team (reduced) |
| **Goal** | Stabilize the system in production |
| **Budget** | CapEx transitioning to OpEx |

**What happens during Hypercare:**
- Monitor system closely for unexpected issues
- Fix critical production bugs fast (same team who built it)
- Validate that real user traffic behaves as expected
- Conduct Knowledge Transfer (KT) to maintenance team
- Hand over documentation, runbooks, and access
- Confirm monitoring and alerting is working

> **Why it exists:** No matter how good UAT is, real production always surprises you. Hypercare ensures the people who know the system best are still available to fix it quickly.

---

### Phase 6: BAU Maintenance (Business As Usual)
> The **ongoing, long-term steady-state support** of a live system after hypercare ends, handled by a permanent small team responsible for keeping the system running reliably.

| | Details |
|---|---|
| **When** | After hypercare ends |
| **Duration** | Permanent — as long as system is live |
| **Who** | Small maintenance / ops team |
| **Goal** | Keep system stable, healthy, and current |
| **Budget** | OpEx (Operational Expenditure) |

**What happens during BAU:**
- Fix bugs reported by users (corrective maintenance)
- Apply security patches and upgrades (adaptive/preventive maintenance)
- Monitor system health — CPU, errors, alerts (preventive maintenance)
- Handle user support tickets
- Small enhancements and configuration changes
- Periodic reviews of system performance

---

## 2. Phase Comparison

| | Sales | Design | Development | UAT | Go-Live | Hypercare | BAU Maintenance |
|---|---|---|---|---|---|---|---|
| **Timing** | Before project | After contract | After design | After dev | After UAT | Right after go-live | After hypercare |
| **Duration** | Weeks–months | Weeks | Months | 2–6 weeks | 1 day | 2–4 weeks | Permanent |
| **Team size** | 2–3 | 4–5 | Full (10) | Reduced (8) | Standby (6) | Skeleton (4) | Minimal (2–3) |
| **Mode** | Propose & win | Plan & design | Build | Validate | Deploy | Intensive support | Steady, routine |
| **Budget** | Sales budget | CapEx | CapEx | CapEx | CapEx | Transitioning | OpEx |

---

## 3. Example: 10-Person Project Team

### Phase 0: Sales — 2-3 People (Pre-Project)

| Role | Count | Responsibility |
|---|---|---|
| Sales / Business Development | 1 | Client relationship, contract negotiation |
| Pre-sales / Solution Consultant | 1 | Propose technical solution, estimate cost |
| Senior Architect (advisory) | 1 | Validate technical feasibility of proposal |

> Note: Sales team is typically **separate from the delivery team**. After contract is signed, they hand over to the PM and BA and move on to the next deal.

---

### Phase 1: Design — 4-5 People

| Role | Count | Responsibility |
|---|---|---|
| Project Manager | 1 | Project setup, planning, stakeholder management |
| Business Analyst | 1 | Detailed requirements gathering |
| Solution Architect | 1 | System and infrastructure design |
| Tech Lead | 1 | Technical design, API contracts, DB schema |
| UI/UX Designer | 1 | Wireframes and prototypes |

---

### Phase 2: Development — Full Team (10 People)

| # | Role | Count |
|---|---|---|
| 1 | Project Manager | 1 |
| 2 | Business Analyst | 1 |
| 3 | Solution Architect | 1 |
| 4 | Tech Lead | 1 |
| 5 | Senior Developer | 2 |
| 6 | Junior Developer | 2 |
| 7 | QA Engineer | 1 |
| 8 | DevOps Engineer | 1 |
| **Total** | | **10** |

---

### Phase 3: UAT — 8 People

| Role | Count | Status | Reason |
|---|---|---|---|
| Project Manager | 1 | ✅ Stays | Manages UAT process |
| Business Analyst | 1 | ✅ Stays | Coordinates UAT with users |
| Solution Architect | 1 | ⚠️ Advisory only | Design complete |
| Tech Lead | 1 | ✅ Stays | Handles UAT defects |
| Senior Developer | 2 | ✅ Stays | Fix UAT bugs |
| Junior Developer | 1 | ⚠️ Reduced to 1 | Less fixing needed |
| QA Engineer | 1 | ✅ Stays | Manages UAT testing |
| DevOps Engineer | 1 | ✅ Stays | Manages UAT environment |

---

### Phase 4: Go-Live — 6 People

| Role | Count | Status | Reason |
|---|---|---|---|
| Project Manager | 1 | ✅ Stays | Oversees go-live |
| Business Analyst | 1 | 🚪 Leaves | Job done after UAT sign-off |
| Solution Architect | 1 | 🚪 Leaves | Moves to next project |
| Tech Lead | 1 | ✅ Stays | Critical go-live support |
| Senior Developer | 2 | ✅ Stays | On standby for issues |
| Junior Developer | 1 | 🚪 Leaves | Moves to next project |
| QA Engineer | 1 | 🚪 Leaves | Testing complete |
| DevOps Engineer | 1 | ✅ Stays | Monitors deployment |

---

### Phase 5: Hypercare — 4 People

| Role | Count | Status | Reason |
|---|---|---|---|
| Project Manager | 1 | 🚪 Leaves | Project closed, moves on |
| Tech Lead | 1 | ✅ Stays | Leads hypercare, conducts KT |
| Senior Developer | 1 | ✅ Stays | Fixes critical production bugs |
| Senior Developer | 1 | 🚪 Leaves | Moves to next project |
| DevOps Engineer | 1 | ✅ Stays | Monitors production |

---

### Phase 6: BAU Maintenance — 2-3 People (Long Term)

| Role | Count | Status | Reason |
|---|---|---|---|
| Tech Lead | 1 | 🚪 Leaves after KT | Hands over and moves to next project |
| Senior Developer | 1 | ✅ Stays permanently | Bug fixes, small enhancements |
| DevOps Engineer | 1 | ✅ Stays permanently | Monitoring, patching, infrastructure |
| App Support | 1 | 🆕 New joiner | Handles user tickets and incidents |

---

## 4. Full Team Timeline Visual

```
Role                  | Sales | Design | Dev  | UAT  | Go-Live | Hypercare | BAU
──────────────────────────────────────────────────────────────────────────────────
Sales / BD            | ████  |        |      |      |         |           |
Pre-sales Consultant  | ████  |        |      |      |         |           |
Project Manager       |       | ████   | ████ | ████ |   ██    |           |
Business Analyst      |       | ████   | ████ | ████ |         |           |
Solution Architect    |       | ████   | ████ | ░░░░ |         |           |
UI/UX Designer        |       | ████   | ░░░░ |      |         |           |
Tech Lead             |       | ████   | ████ | ████ |   ██    |  ████████ |
Senior Dev (leaves)   |       |        | ████ | ████ |   ██    |           |
Senior Dev (stays)    |       |        | ████ | ████ |   ██    |  ████████ | ████
Junior Dev (1)        |       |        | ████ | ████ |         |           |
Junior Dev (2)        |       |        | ████ | ░░░░ |         |           |
QA Engineer           |       |        | ████ | ████ |         |           |
DevOps Engineer       |       | ░░░░   | ████ | ████ |   ██    |  ████████ | ████
App Support (new)     |       |        |      |      |         |           | ████

████ = Active    ░░░░ = Advisory/Reduced    (blank) = Not on project
```

---

## 5. Where Does Everyone Go?

| Person | After Project | Next Destination |
|---|---|---|
| **Sales / BD** | ➡️ Next deal | Moves on after contract signed |
| **Pre-sales Consultant** | ➡️ Next proposal | Joins next sales opportunity |
| **Project Manager** | ➡️ Next project | Assigned by PMO to incoming project |
| **Business Analyst** | ➡️ Next project | Starts requirements gathering |
| **Solution Architect** | ➡️ Next project | Joins design phase of next project |
| **UI/UX Designer** | ➡️ Next project | Joins design phase of next project |
| **Tech Lead** | ➡️ Next project | Becomes Tech Lead on next project |
| **Senior Dev (left)** | ➡️ Next project | Developer on next project |
| **Junior Devs** | ➡️ Next project | Gain experience on next project |
| **QA Engineer** | ➡️ Next project | Test planning for next project |
| **Senior Dev (stayed)** | 🏠 This system | Owns system in BAU permanently |
| **DevOps Engineer** | 🏠 Multiple systems | Supports this and other live systems |
| **App Support** | 🏠 This system | New hire or internal transfer for BAU |

---

## 6. Key Takeaways

> Out of **10 development people**, typically only **2-3 stay long term** for BAU maintenance.

### The Golden Rules of Team Transition

1. **Sales team leaves** as soon as contract is signed — they never join delivery
2. **UI/UX Designer leaves** after design phase — no ongoing role in coding
3. **BA and QA leave earliest** from delivery — job ends at UAT sign-off
4. **Architect leaves early** — design is complete at development phase
5. **PM leaves at go-live** — project is delivered, closure report done
6. **Most developers leave after hypercare** — once system is stable
7. **DevOps stays the longest** — systems always need infrastructure support
8. **1 Senior Dev stays** — someone needs to know the system deeply
9. **App Support joins new** — often a new person takes on BAU tickets

### The Biggest Risk
> Knowledge loss when developers leave. Always ensure proper **Knowledge Transfer (KT)** and documentation during hypercare before the team reduces to BAU size.

---

## 7. Simple Analogy

> Think of **building and running a restaurant:**
>
> - **Sales** = Business developer pitches the restaurant concept to investors and secures funding
> - **Design** = Architects and interior designers plan the layout, kitchen, and menu concept
> - **Development** = Contractors, chefs, and staff all work together to build and set up the restaurant
> - **UAT** = Trial runs with selected guests before official opening
> - **Go-Live** = Grand opening day — all hands on deck
> - **Hypercare** = First few weeks — head chef and full team watching every dish, fixing mistakes immediately
> - **BAU Maintenance** = Normal operations — smaller regular team runs the kitchen daily following established processes

---

*Reference guide based on industry-standard enterprise software project lifecycle practices.*
