# Collaborative Decision-Making Workflow

This document describes how to work with ClaudeCode on architectural and framework decisions using the human-in-the-loop templates.

**Version:** 1.0 (2025-10-13)

---

## Table of Contents

1. [When to Use This Workflow](#when-to-use-this-workflow)
2. [Quick Start Guide](#quick-start-guide)
3. [Detailed Workflow](#detailed-workflow)
4. [File Naming Conventions](#file-naming-conventions)
5. [What Claude Should Do](#what-claude-should-do)
6. [Examples from Practice](#examples-from-practice)
7. [Troubleshooting](#troubleshooting)
8. [Future Enhancements](#future-enhancements)
9. [Glossary](#glossary)
10. [Maintenance](#maintenance)

---

## When to Use This Workflow

### ✅ Use Human-in-the-Loop for:

- **Architecture and framework definitions** - Layer boundaries, folder structures, component responsibilities
- **Terminology and naming conventions** - What to call things throughout the methodology
- **Design patterns and paradigm choices** - Which architectural patterns to adopt
- **Strategic decisions** - Choices that affect many downstream decisions
- **"Multiple ways to do this" situations** - When there are valid alternatives with trade-offs

### ❌ Don't Use This Workflow for:

- **Simple bug fixes** - One clear solution
- **Implementing already-decided designs** - Decisions already documented
- **Straightforward documentation updates** - Typos, formatting, cross-references
- **Unambiguous best practices** - Clear industry standard applies

### Key Principle

**This repository defines methodology, not implements it.** Most work involves architectural choices that require intimate human collaboration, not autonomous implementation.

---

## Quick Start Guide

### For Contributors (Manual Process)

**1. Copy the human-in-loop template:**
```bash
# From your terminal
cp plan/templates/hitl-decision-template.md plan/_my-task-hitl-prompt.md
```

**2. Customize it:**
Open `plan/_my-task-hitl-prompt.md` and fill in:
- `[TASK NAME]` - Descriptive name for the task
- **Task-Specific Context** section - Requirements, constraints, background
- **Documents to Review** section - List files Claude should read
- Adjust rigor level if needed (see template README for options)

**3. Start Claude Code session:**
- Open new Claude Code session
- Paste the entire customized prompt
- Wait for Claude to acknowledge and begin decision discovery

**4. Collaborate on decisions:**
- Claude creates `plan/_my-task-decisions-needed.md`
- Review each decision with options and trade-offs
- Make your choices and provide rationale
- Claude updates the document with resolved decisions

**5. Implementation proceeds:**
- After all decisions resolved, Claude implements based on your choices
- Decisions document becomes permanent record

---

## Detailed Workflow

### Phase 1: Setup (You)

**1.1 Create prompt file:**
```bash
# Naming convention: _[task-name]-hitl-prompt.md
# Examples:
#   plan/_framework-structure-hitl-prompt.md
#   plan/_terminology-choices-hitl-prompt.md
#   plan/_session-03-code-paradigms-hitl-prompt.md
```

**1.2 Fill in required sections:**

```markdown
# Task: [Your Descriptive Task Name]

[Keep all the template instructions - don't delete them]

---

## Task-Specific Context

**Background:**
[Why are we doing this? What's the problem?]

**Requirements:**
[What needs to be accomplished?]

**Constraints:**
[Any limitations or requirements to consider?]

**Success Criteria:**
[How will we know this is done well?]

## Documents to Review

[List files Claude should read before making recommendations]

**Must Read:**
- `/path/to/critical-doc.md` - Why this matters
- `/path/to/analysis.md` - Background analysis

**Should Read:**
- `/path/to/related-work.md` - Related context
- `/docs/lean-clean-axioms.md` - Established principles

**Optional:**
- `/path/to/reference.md` - Additional context if needed

## Ready?

Please begin with **Decision Discovery** - identify all architectural decisions before we proceed with implementation.
```

**1.3 Adjust rigor level (optional):**

See `/plan/templates/hitl-decision-template.README.md` for:
- **More lightweight** - Remove recommendations, allow low-impact autonomous decisions
- **More rigorous** - Add decision dependencies, rollback cost estimates, research citations

### Phase 2: Decision Discovery (Claude)

**2.1 Claude acknowledges mode:**

```
"I understand I'm in Human-in-the-Loop Decision Mode. I will:
1. Identify ALL decisions upfront
2. Present options with trade-offs
3. Wait for your approval before implementing"
```

**2.2 Claude reads context:**

- All documents listed in "Documents to Review"
- Related axioms, prior decisions, analysis docs
- Existing framework/methodology documents

**2.3 Claude creates decisions document:**

File: `plan/_[matching-task-name]-decisions-needed.md`

Uses template: `/plan/templates/decision-document-template.md`

Structure:

```markdown
# [Task Name]: Decisions Needed

**Status:** 🚧 0 of N Resolved | N Awaiting Discussion

## Decision 1: [Name] ⚠️ CRITICAL

**What I did:**
[Describe approach taken, if any work was done before decision was identified]

**Context:**
[Relevant background - what have we done before? What do docs say?]

**Question for you:**
[Clear framing of what needs to be decided]

- [ ] **Option A: [Name]** ([brief descriptor])
  - [Brief description of this approach]
  - ✅ Pro: [Benefit]
  - ✅ Pro: [Benefit]
  - ❌ Con: [Drawback]
  - ❌ Con: [Drawback]

- [ ] **Option B: [Name]** ([brief descriptor])
  - [Brief description of this approach]
  - ✅ Pro: [Benefit]
  - ❌ Con: [Drawback]

**My recommendation:** Option A

**Reasoning:**
[Why I think this option best fits the requirements/context]

**Your decision:** [AWAITING INPUT]

**Rationale:**
[Space for you to fill in after deciding]

**Impact:** HIGH - [Brief description of what this affects]

**Status:** 🚧 AWAITING

[... more decisions following same structure ...]

## Summary of Decisions
[Table with all decisions, priorities, and statuses]

## Next Steps
[Completed work, awaiting input, after decisions resolved]
```

**2.4 Claude presents to you:**
- "I've identified N decisions that need to be made"
- "I've created a decisions document at `plan/_[task]-decisions-needed.md`"
- "Please review each decision and let me know your choices"

### Phase 3: Collaborative Decision-Making (You + Claude)

**3.1 You review decisions:**
- Read context and options for each decision
- Consider trade-offs and Claude's recommendations
- Evaluate impact on your goals

**3.2 You make choices:**

For each decision, provide:
```markdown
**Your decision:** ✅ **Option B: [Name]**

**Rationale:**
[Why you chose this option]
[What trade-offs you're accepting]
[How this aligns with methodology goals]
```

**3.3 Claude updates document:**
- Marks decisions as `✅ RESOLVED`
- Records your rationale
- Updates summary table
- Checks for decision dependencies

**3.4 Handle new decisions:**

If Claude discovers new decisions during discussion:
```markdown
⚠️ NEW DECISION DISCOVERED

## Decision N+1: [Name]
**Why this wasn't identified earlier:**
[Explanation of discovery]

[... full decision structure ...]
```

Claude should:
- ❌ STOP and present the new decision
- ✅ Wait for your input
- ✅ Update decisions document
- ✅ Continue only after resolved

### Phase 4: Implementation (Claude)

**4.1 All decisions resolved:**
- Summary table shows all `✅ RESOLVED`
- Claude confirms: "All N decisions resolved, proceeding with implementation"

**4.2 Claude implements:**
- Creates/updates files based on documented decisions
- References decisions document when making choices
- Maintains consistency with your rationale

**4.3 Decisions document persists:**
- Committed to repository as permanent record
- Referenced in future sessions
- Used to inform related work

---

## File Naming Conventions

### For Prompt Files (You Create)

**Pattern:** `plan/_[task-name]-hitl-prompt.md`

**Examples:**
- `plan/_session-02-framework-hitl-prompt.md`
- `plan/_ports-adapters-terminology-hitl-prompt.md`
- `plan/_test-structure-decisions-hitl-prompt.md`

**Rules:**
- Prefix with underscore (`_`) to indicate working document
- Use kebab-case for task name
- Suffix with `-hitl-prompt` to indicate it's a human-in-loop prompt
- `.md` extension

### For Decisions Documents (Claude Creates)

**Pattern:** `plan/_[matching-task-name]-decisions-needed.md`

**Examples:**
- `plan/_session-02-framework-decisions-needed.md` (matches `_session-02-framework-hitl-prompt.md`)
- `plan/_ports-adapters-terminology-decisions-needed.md`
- `plan/_test-structure-decisions-decisions-needed.md`

**Rules:**
- Same task name as the prompt file (maintains pairing)
- Suffix with `-decisions-needed` to indicate it's a decisions artifact
- Claude should infer the name from the prompt file name if possible

### File Pairing

```
plan/
├─ _session-02-framework-hitl-prompt.md          # You create & customize
├─ _session-02-framework-decisions-needed.md     # Claude creates during session
├─ _terminology-choices-hitl-prompt.md           # You create & customize
└─ _terminology-choices-decisions-needed.md      # Claude creates during session
```

The naming makes the relationship obvious: same prefix, different suffix.

---

## What Claude Should Do

### When Claude Sees a Human-in-Loop Prompt

**Recognition:**
- File contains "Decision-Making Mode: Human-in-the-Loop ⚠️"
- File is based on `/plan/templates/hitl-decision-template.md`

**Required Actions:**

1. **✅ Acknowledge the mode explicitly:**
   ```
   "I understand I'm in Human-in-the-Loop Decision Mode. I will not make
   unilateral architectural decisions. I will identify all decisions upfront
   and present options with trade-offs before implementing."
   ```

2. **✅ Read all specified documents:**
   - Everything in "Documents to Review" section
   - Related axioms: `/docs/lean-clean-axioms.md`
   - Prior decision documents if referenced

3. **✅ Perform thorough analysis:**
   - Understand task requirements fully
   - Identify ALL architectural decisions that need to be made
   - Research context and prior work
   - Consider decision dependencies

4. **✅ Create decisions document:**
   - Use `/plan/templates/decision-document-template.md`
   - Name it to match the prompt file (replace `-hitl-prompt` with `-decisions-needed`)
   - Fill in all decision entries with:
     - Clear context
     - 2-3 viable options
     - Detailed pros/cons
     - Your recommendation (marked as suggestion)
     - Impact assessment

5. **✅ Present to user before implementing:**
   - "I've identified N decisions"
   - "I've created decisions document at [path]"
   - "Please review and provide your choices"
   - DO NOT start implementation

6. **✅ Wait for user input:**
   - User reviews each decision
   - User chooses options
   - User provides rationale

7. **✅ Document user decisions:**
   - Update decisions document with user choices
   - Record user rationale verbatim
   - Mark decisions as `✅ RESOLVED`
   - Update summary table

8. **✅ Implement based on decisions:**
   - Only after all decisions resolved
   - Follow user choices exactly
   - Reference decisions document when making implementation choices
   - If new decisions arise, STOP and present them

### Anti-Patterns Claude Should Avoid

❌ **Don't create files before presenting decisions**
- Bad: "I created the framework document with this structure"
- Good: "I've identified 12 decisions about framework structure, let me present them"

❌ **Don't make assumptions about choices**
- Bad: "I'll use Option A since it's the industry standard"
- Good: "Here are Options A, B, C with trade-offs - which do you prefer?"

❌ **Don't discover decisions incrementally**
- Bad: "Let's decide this first... okay now there's another decision..."
- Good: "I've identified ALL 12 decisions upfront, here they are"

❌ **Don't proceed without explicit approval**
- Bad: "I recommend Option A, proceeding with implementation"
- Good: "I recommend Option A because [reasons], what's your decision?"

❌ **Don't bury decisions in implementation**
- Bad: "I implemented X, is that okay?" (too late)
- Good: "Before implementing, should we use X or Y? Here are trade-offs"

### If Claude Forgets

If Claude starts implementing before presenting decisions, interrupt with:

```
STOP. You are in Human-in-the-Loop Decision Mode.
You should NOT be implementing yet.

Please:
1. Identify ALL decisions that need to be made
2. Create a decisions document with options and trade-offs
3. Wait for my choices before implementing

See /plan/templates/hitl-decision-template.README.md for the process.
```

---

## Examples from Practice

### Example 1: Session 02 Framework Structure

**Context:** Define folder structures for three PoC types (Steel Thread, Pragmatic CA, Full CA)

**What happened:**
- Started with 8 decisions identified
- After reviewing `CLEAN_ARCHITECTURE_ANALYSIS.md`, discovered 5 more
- Required creating decisions document retroactively
- Total: 12 architectural decisions

**Decisions included:**
- **Critical:** "Orchestrator" vs "Controller" terminology
- **High:** Does `drivers/` layer exist?
- **Medium:** Gateways vs Repositories/Services terminology
- **Medium:** DTO layer strategy
- **Medium:** Ports location and naming

**Outcome:**
- 7 decisions resolved collaboratively
- 5 awaiting discussion
- Decisions document: `/plan/_session-02-decisions-needed.md`
- Zero rework because decisions were explicit

**Key lesson:** Upfront decision discovery prevents implicit choices and rework

### Example 2: Terminology Consistency (Hypothetical)

**Task:** Establish consistent terminology across methodology docs

**Prompt file:** `plan/_terminology-consistency-hitl-prompt.md`

**Decisions document:** `plan/_terminology-consistency-decisions-needed.md`

**Potential decisions:**
1. "Use Case" vs "User Story" vs "Feature"
2. "Adapter" vs "Gateway" vs "Service"
3. "Port" vs "Interface" vs "Contract"
4. Stakeholder-facing vs implementation language
5. Evolution path: when does terminology change?

**Process:**
- Claude reads all existing docs to find terminology usage
- Identifies inconsistencies and choices needed
- Presents options with trade-offs (consistency vs familiarity vs precision)
- Human chooses based on methodology goals
- Claude updates docs to match decided terminology

### Example 3: Test Strategy Decisions (Hypothetical)

**Task:** Define testing approach for methodology examples

**Prompt file:** `plan/_test-strategy-hitl-prompt.md`

**Documents to review:**
- Session 02 framework document (test structure already defined)
- TDD best practices docs
- Acceptance criteria templates

**Potential decisions:**
1. Acceptance test style: Given/When/Then vs Arrange/Act/Assert
2. Mock strategy: Fakes only vs Mocks + Fakes
3. Test organization: Mirror app structure vs feature-based
4. Integration test scope: Which adapters need real tests?
5. Test naming convention: `test_*` vs descriptive sentences

**Why human-in-loop:**
- Testing is strategic - affects development workflow
- Multiple valid approaches with trade-offs
- Decision impacts developer experience
- Choices should align with methodology philosophy (Lean, Clean, Agentic)

---

## Troubleshooting

### "Claude started implementing before presenting decisions"

**Interrupt immediately:**
```
STOP. You're in Human-in-the-Loop Decision Mode.
Please create a decisions document before implementing.
See /plan/WORKFLOW.md for the process.
```

**Then:** Have Claude read this document and start over with decision discovery.

### "Claude only presented 3 decisions, but I know there are more"

**Prompt for completeness:**
```
Please do a more thorough analysis. Read [specific docs] and identify
ALL architectural decisions, including:
- Terminology choices
- Folder structure and naming
- Layer responsibilities and boundaries
- Interface definitions and patterns

Present ALL decisions before we proceed.
```

### "Decisions document is incomplete - missing context/options"

**Request detail:**
```
For Decision X, please provide:
- More context: what have we done before? what do related docs say?
- More options: I see 2 options, are there other viable approaches?
- Better trade-offs: what are the long-term implications of each choice?
```

### "I want to adjust the rigor level for future sessions"

**See:** `/plan/templates/hitl-decision-template.README.md`

**Options:**
- **Less rigor:** Remove recommendations, allow low-impact autonomous decisions
- **More rigor:** Add decision dependencies, rollback costs, implementation time estimates

**Customize the template** before pasting into Claude for next session.

### "How do I resume a session with partial decisions?"

**In new session, provide context:**
```
# Resuming Session: [Task Name]

**Previous Work:**
- Decisions document: /plan/_task-decisions-needed.md
- Status: X of Y decisions resolved

**Today's Goal:**
- Resolve remaining Y decisions
- Proceed with implementation after all decisions made

**Instructions:**
1. Read the decisions document
2. Present the unresolved decisions (those marked 🚧 AWAITING)
3. Wait for my choices
4. Update document with resolved decisions
5. Then proceed with implementation

Please begin by reviewing the decisions document.
```

---

## Future Enhancements

### Potential Tooling (Not Yet Implemented)

**1. Scaffolding Script:**
```bash
./scripts/new-decision-session.sh task_name
# Creates: plan/_task-name-hitl-prompt.md
# Prompts for: context, documents to review
# Outputs: customized prompt ready to paste
```

**2. Validation Hook:**
```bash
./scripts/validate-decisions.sh plan/_task-decisions-needed.md
# Checks: all decisions have status (resolved or awaiting)
# Checks: resolved decisions have rationale filled in
# Checks: summary table matches decision entries
```

**3. Session Resume Helper:**
```bash
./scripts/resume-session.sh task_name
# Reads: plan/_task-decisions-needed.md
# Outputs: resume prompt with current state
```

**Status:** These are ideas for future improvement after validating the manual workflow through several sessions.

---

## Glossary

Common abbreviations and terms used in this workflow:

### Abbreviations

- **HITL**: Human-In-The-Loop - A collaborative decision-making mode where the AI presents options and waits for human choices before proceeding
- **CA**: Clean Architecture - Robert C. Martin's architectural pattern with layered dependencies pointing inward
- **PoC**: Proof of Concept - A working prototype demonstrating feasibility
- **DTO**: Data Transfer Object - Simple data structures for transferring information between layers
- **TDD**: Test-Driven Development - Write tests before implementation code
- **BDD**: Behavior-Driven Development - Acceptance criteria in Given/When/Then format

### Methodology Terms

- **Steel Thread**: Minimal end-to-end slice proving viability (simplest PoC type)
- **Pragmatic CA**: Moderate Clean Architecture implementation balancing speed and structure
- **Full CA**: Complete Clean Architecture with all layers and patterns (most rigorous PoC type)
- **Orchestrator**: Component that coordinates use cases and formats responses (Interface Adapters layer)
- **Use Case**: Business rule implementation in Application layer
- **Adapter**: Implementation of external integration (Infrastructure layer)
- **Port**: Interface defining contract for adapters (also called "Infrastructure Interface")
- **Gateway**: Vendor integration adapter (e.g., OpenAI gateway, S3 gateway)
- **Repository**: Persistence adapter (e.g., database repository)

### File Naming Patterns

- **`_prefix`**: Underscore prefix indicates working/planning document (not final deliverable)
- **`-hitl-prompt`**: Human-in-loop prompt file you customize and paste into Claude
- **`-decisions-needed`**: Decisions document Claude creates during collaboration
- **kebab-case**: Use kebab-case for file names (e.g., `my-task-name.md`)
- **snake_case**: Use snake_case for code identifiers (e.g., `my_variable_name`)

### Workflow States

- **🚧 AWAITING**: Decision not yet resolved, waiting for human input
- **✅ RESOLVED**: Decision made and documented with rationale
- **⚠️ CRITICAL**: High-priority decision with cascading impact

---

## Maintenance

**This document should be updated when:**
- New templates are added to `/plan/templates/`
- Workflow pain points are discovered through practice
- Better naming conventions or file structures are identified
- Additional anti-patterns or troubleshooting cases emerge
- New abbreviations or terms are introduced to the methodology

**Version History:**
- v1.0 (2025-10-13): Initial workflow documentation based on Session 02 learnings

**Related Documentation:**
- `/plan/templates/hitl-decision-template.README.md` - Template usage guide
- `/plan/templates/decision-document-template.README.md` - Decisions artifact guide
- `/CLAUDE.md` - Repository overview and default working mode
- `/docs/lean-clean-axioms.md` - Established architectural principles
