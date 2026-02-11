# Agent Communication Protocol

How agents talk to each other — simulating a real dev team.

## 3 Communication Channels

### Channel 1: Handoffs (One-way, structured)

**When**: Agent A finishes a phase, Agent B takes over.
**Format**: `features/FEAT-XXX/handoffs/HANDOFF-latest.md`
**Direction**: Always A → PM → B (PM routes, scoped to feature)

```
BA finishes requirement → HANDOFF → PM reads → triggers Architect
Architect finishes design → HANDOFF → PM reads → creates task cards (labeled backend/frontend)
Backend Dev finishes task → HANDOFF → PM reads → triggers Reviewer
Frontend Dev finishes task → HANDOFF → PM reads → triggers Reviewer
Reviewer approves → HANDOFF → PM reads → triggers next task or Tester
Tester finishes → HANDOFF → PM reads → triggers QA
```

This is the **pipeline flow** — sequential and structured.

### Channel 2: Discussions (Multi-way, conversational)

**When**: An agent has a question, concern, or needs input from another agent.
**Format**: Within-feature → `features/FEAT-XXX/discussions/DISC-XXX.md` | Cross-feature → `discussions/DISC-CROSS-XXX.md`
**Direction**: Any agent → Any agent (PM moderates)

Discussions simulate real team conversations:
- Reviewer asks Backend Dev about a design choice
- Reviewer asks Frontend Dev about a UI pattern
- Backend Dev asks Architect about design feasibility
- Frontend Dev asks Backend Dev about API contract
- Tester asks BA to clarify acceptance criteria
- Backend Dev flags a concern to Architect about the design
- Frontend Dev asks Architect about component boundaries
- QA asks everyone about a cross-cutting concern
- Any agent requests Researcher for technical investigation

**Discussion Flow:**
```
1. Agent A writes DISC-XXX.md with question + context
2. Agent A marks it: Status: OPEN, Participants: [who needs to respond]
3. PM sees open discussion → routes to participant
4. Participant reads discussion → writes response entry
5. If resolved → Status: RESOLVED + summary of decision
6. If needs more input → another round (max 3 rounds)
7. PM updates relevant docs with the decision
```

**Discussion Rules:**
- Each entry has: `### [Agent Name] → [Target Agent]` header
- Keep entries concise — problem + options + recommendation
- Max 3 rounds per discussion — then PM escalates to human
- Decisions from discussions go into `decisions/ADR-XXX.md`
- Any agent can open a discussion, not just PM

### Channel 3: Review Comments (Feedback on work product)

**When**: Reviewer or QA finds issues in code/tests/design.
**Format**: `features/FEAT-XXX/reviews/TASK-XXX-review.md` or `features/FEAT-XXX/reviews/qa-report.md`
**Direction**: Reviewer → Backend Dev or Frontend Dev (via PM), QA → any agent (via PM)

Review comments simulate PR review comments:
- Tagged by severity: `[CRITICAL]`, `[WARNING]`, `[SUGGESTION]`, `[QUESTION]`
- Include file path + line number when possible
- Include "current code" and "suggested fix"
- `[QUESTION]` comments automatically open a discussion thread

**Review Cycle:**
```
1. Reviewer writes review with comments
2. If APPROVED → next phase
3. If CHANGES_REQUESTED:
   a. PM sends review to the appropriate Dev (Backend Dev or Frontend Dev based on task label)
   b. Dev reads comments, fixes code
   c. Dev writes response: "Fixed" / "Won't fix because..." / "Need discussion"
   d. Reviewer re-reviews ONLY changed parts
   e. Max 3 rounds → then PM escalates to human
```

---

## Common Discussion Scenarios

### Scenario 1: Design Feasibility (Backend Dev ↔ Architect)
```
Architect creates design → Backend Dev reads it → spots a problem
Backend Dev opens DISC: "The design says use WebSocket, but our infra doesn't support it"
Architect responds: "Good catch. Let's use SSE instead"
Decision → ADR-XXX: "Use SSE over WebSocket due to infra constraints"
```

### Scenario 2: Acceptance Criteria Ambiguity (Tester ↔ BA)
```
Tester reads spec → unclear what "recent orders" means
Tester opens DISC: "Does 'recent' mean last 7 days, 30 days, or configurable?"
BA responds: "Last 30 days, not configurable for now. Added to requirement."
Decision → requirement doc updated
```

### Scenario 3: Code Review Debate (Reviewer ↔ Backend Dev ↔ Architect)
```
Reviewer comments: "This should use a generator instead of loading all into memory"
Backend Dev responds: "Generator won't work because downstream needs random access"
Reviewer: "Then paginate with cursor. Let's ask Architect."
Architect: "Cursor pagination. Max 100 per page."
Decision → implementation updated
```

### Scenario 4: Cross-cutting Concern (QA ↔ All)
```
QA finds: "3 services are doing auth checks differently"
QA opens DISC with all: "We need a consistent auth pattern"
Architect: "Create a shared middleware. Here's the pattern..."
Decision → ADR + new task card for standardization
```

### Scenario 5: API Contract (Frontend Dev ↔ Backend Dev)
```
Frontend Dev reads API spec → needs a field not in the response
Frontend Dev opens DISC: "I need `user.avatar_url` in the /me endpoint response"
Backend Dev responds: "Can add it. Will include in the UserProfile schema."
Decision → design.md updated, new task card if needed
```

### Scenario 6: Technical Research (Any Agent → Researcher via PM)
```
Architect is unsure about caching strategy → asks PM for research
PM triggers Researcher: "Compare Redis vs in-memory caching for our session store"
Researcher investigates → writes RESEARCH-001.md with comparison + recommendation
PM routes findings back to Architect
Architect incorporates recommendation into design
```

---

## Discussion File Structure

```
.ai-workspace/features/FEAT-XXX/discussions/
├── DISC-001.md        ← Design feasibility discussion
├── DISC-002.md        ← Acceptance criteria clarification
├── DISC-003.md        ← API contract discussion
├── RESEARCH-001.md    ← Researcher's technical report
└── RESEARCH-002.md    ← Another research report

.ai-workspace/discussions/
├── DISC-CROSS-001.md  ← Cross-feature file conflict resolution
└── DISC-CROSS-002.md  ← Cross-cutting auth pattern
```

## When to Open a Discussion vs. Just Handling It

**Open a discussion when:**
- You're uncertain about the right approach
- The issue affects more than your current task
- You disagree with a design or implementation choice
- You need information from another agent's domain
- A decision could have architectural implications
- You need Researcher to investigate something (request via PM)

**Just handle it when:**
- It's a simple bug fix with an obvious solution
- It's a typo or naming inconsistency
- The conventions doc already has the answer
- It's within your domain and doesn't affect others

---

## PM's Role in Communication

PM is the **moderator**, not a bottleneck:

1. **Route**: PM reads open discussions, triggers the right agent to respond
2. **Escalate**: If discussion exceeds 3 rounds → PM escalates to human
3. **Record**: PM ensures decisions are captured in ADR or requirement updates
4. **Summarize**: PM provides human with discussion summaries at checkpoints
5. **Unblock**: If agents disagree → PM presents options to human for decision
6. **Research**: PM triggers Researcher when any agent requests technical investigation

PM does NOT:
- Answer technical questions on behalf of agents
- Override agent recommendations
- Close discussions without resolution
