# Decision Document Template

**Purpose:** This template is for the **decisions artifact** created during collaborative work with ClaudeCode. It tracks architectural decisions, presents options, documents choices, and serves as the single source of truth for why decisions were made.

---

## Relationship to Human-in-the-Loop Template

These two templates work together:

1. **Human-in-the-Loop Template** (`hitl-decision-template.md`)
   - You fill out and paste into Claude to start a session
   - Sets expectations for collaborative decision-making
   - Instructs Claude on the process to follow

2. **Decision Document Template** (`decision-document-template.md`) ← THIS TEMPLATE
   - Claude creates this during the decision discovery phase
   - Living document that tracks all decisions for the task
   - Collaboratively filled in as decisions are made
   - Becomes permanent record of architectural choices

**Workflow:**
```
You: Paste customized human-in-loop template into Claude
  ↓
Claude: Analyzes task, researches context
  ↓
Claude: Creates decisions document using THIS template
  ↓
Claude: Presents decisions document with options and trade-offs
  ↓
You: Review each decision, choose options
  ↓
Claude: Updates decisions document with your choices and rationale
  ↓
Claude: Implements based on documented decisions
  ↓
Result: Decisions document becomes permanent record in /plan/ folder
```

---

## Usage Notes

### When Claude Creates This Document

After you provide a task using the human-in-loop template, Claude should:

1. **Analyze the task thoroughly** - Read relevant docs, axioms, prior work
2. **Identify all architectural decisions** - Every choice point that needs your input
3. **Create a decisions document** using this template
4. **Present it to you** with all options and trade-offs laid out
5. **Wait for your input** on each decision before implementing

### What Goes in This Document

**For each decision:**
- Clear description of what needs to be decided
- Why it matters (impact on the work)
- Context from relevant documents or prior decisions
- 2-3 options with detailed trade-offs (pros/cons)
- Claude's recommendation with reasoning (clearly marked as suggestion)
- Space for your decision
- Space for your rationale after choosing

**Summary section:**
- Table showing all decisions with status (resolved vs awaiting)
- Priority levels (CRITICAL, HIGH, MEDIUM, LOW)
- Quick reference of what's been decided vs what's still open

**Next steps:**
- What happens after all decisions are resolved
- Implementation plan
- Documentation updates needed

### Document Lifecycle

1. **Created**: Claude creates during decision discovery phase
2. **In Progress**: Decisions marked as "AWAITING INPUT" as options are presented
3. **Resolved**: As you make choices, decisions marked with "✅ RESOLVED" and your rationale
4. **Complete**: All decisions resolved, implementation can proceed
5. **Permanent Record**: Saved in `/plan/` as reference for future sessions

### File Naming Convention

Use descriptive names that indicate the task:

- `_session-XX-decisions-needed.md` - For session-based work
- `_framework-structure-decisions.md` - For specific architectural topics
- `_terminology-decisions.md` - For naming/terminology choices

The underscore prefix (`_`) indicates working/planning documents vs final artifacts.

---

## Lessons from Session 02

This template emerged from Session 02 experience with framework folder structures:

**What Happened:**
- Claude created framework document with implicit choices
- Initially identified 8 decisions
- After reviewing analysis docs, discovered 5 more critical decisions
- Had to create decisions document retroactively to track all choices

**What This Template Fixes:**
- Standardizes decision document format
- Ensures consistent structure for presenting options
- Provides clear status tracking (resolved vs awaiting)
- Creates audit trail of decision rationale
- Separates in-progress decisions from resolved ones

**Key Insight:**
Decision documents are living artifacts that:
- Start sparse (known decisions)
- Grow as new decisions are discovered
- Become complete reference after all choices are made
- Enable resuming work across sessions without losing context

---

## Template Structure

The template provides:

### Header Section
- Task name and overall status
- Quick summary of resolved vs awaiting decisions
- Update notes (as new decisions are discovered)

### Decision Entries
Each decision includes:
- Title with priority indicator (⚠️ CRITICAL if applicable)
- "What I did" - Claude's initial approach (if applicable)
- "Context" - Relevant background from docs/analysis
- "Question for you" - Clear framing of the choice
- "Options" - 2-3 alternatives with pros/cons
- "Your decision" - Space for your choice
- "Rationale" - Space for why you chose it
- "Impact" - HIGH/MEDIUM/LOW assessment
- "Status" - ✅ RESOLVED or 🚧 AWAITING

### Summary Table
Quick reference showing:
- Decision number and name
- Priority level
- Status (resolved/awaiting)

### Next Steps
- What's completed
- What's awaiting input
- What happens after decisions are made

---

## Customization

You can adjust the template for different decision types:

**For lightweight decisions:**
- Remove "What I did" section if Claude hasn't started yet
- Simplify to just Options and Trade-offs
- Skip Impact assessment for low-priority choices

**For critical architectural decisions:**
- Add "Dependencies" - Which decisions affect others
- Add "Rollback cost" - How hard to change later
- Add "Research citations" - Link to docs/analysis that informed options
- Add "Implementation estimate" - Time impact for each option

**For terminology decisions:**
- Add "Consistency check" - Where else this term appears
- Add "Stakeholder impact" - Who sees this terminology
- Add "Evolution path" - How this choice affects future work

---

## Template Maintenance

**Location:**
- Bare template: `/plan/templates/decision-document-template.md`
- Usage instructions/versioning: `/plan/templates/decision-document-template.README.md` (this file)

**Version:** 1.0 (2025-10-13)

**Update this template when:**
- You discover better ways to structure decisions
- You find additional metadata that should be tracked
- You learn lessons from collaborative sessions
- You need different formats for different decision types

---

## Related Templates

- `/plan/templates/hitl-decision-template.md` - The prompt template you give to Claude
- `/plan/templates/session-starter-template.md` - For resuming work across sessions (if exists)
- (Add more as methodology evolves)

---

## Example Usage

**Session Start:**
```markdown
You to Claude: [Paste customized human-in-loop template]

Claude: "I'll analyze the task and identify all architectural decisions.
        Let me create a decisions document..."

Claude: [Creates document using decision-document-template.md]
        [Presents completed document with all options laid out]

You: [Review decisions, choose options, provide rationale]

Claude: [Updates document with ✅ RESOLVED status and your rationale]
        [Proceeds with implementation based on documented decisions]
```

**Result:**
- Zero implementation before decisions are made
- All trade-offs visible before choosing
- Clear record of why each choice was made
- Can resume across sessions without losing context
