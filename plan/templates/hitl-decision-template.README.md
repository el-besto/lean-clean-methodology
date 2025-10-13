# Human-in-the-Loop Decision-Making Template

**Purpose:** Use this template when you want ClaudeCode to involve you intimately in the decision-making process rather than making unilateral choices.

---

## When to Use This Template

Use this template for:

- Architecture and framework definitions
- Folder structure and naming conventions
- Design patterns and paradigm choices
- Any work where "there are multiple ways to do this"
- Strategic decisions that affect many downstream choices

DON'T use this template for:

- Simple bug fixes with one clear solution
- Implementing already-decided designs
- Straightforward documentation updates
- Tasks where best practices are unambiguous

## Usage Notes

### How to Use This Template

**Quick Start:**

1. **Copy the template:** `/plan/templates/hitl-decision-template.md`
2. **Customize these sections:**
   - Task name and description
   - Task-Specific Context (Background, Requirements, Constraints, Success Criteria)
   - Documents to Review (Must Read, Should Read, Optional)
   - Rigor level adjustments (see "Adjusting the Template" below)
3. **Paste into ClaudeCode** at the start of your session
4. **Claude creates decisions document** using `/plan/templates/decision-document-template.md`
5. **Collaborate on decisions** - Review options, make choices, provide rationale
6. **Implementation proceeds** based on your documented decisions

**For detailed workflow process:** See `/plan/WORKFLOW.md` for complete 4-phase workflow, file naming conventions, troubleshooting, and examples.

### When Claude Forgets

If Claude starts implementing before presenting decisions:

**Interrupt with:**

```
STOP. You are in Human-in-the-Loop Decision Mode.
You should NOT be implementing yet.

Please:
1. Identify ALL decisions that need to be made
2. Present them to me with options and trade-offs
3. Wait for my choices before implementing
```

### Adjusting the Template

You can adjust the rigor level:

**More Lightweight (faster):**

- Remove "My recommendation" - just present options
- Allow Claude to make low-impact decisions autonomously
- Focus on architectural decisions only

**More Rigorous (slower but thorough):**

- Require research citations for each option
- Add "Decision dependencies" section (which decisions affect others)
- Require Claude to estimate implementation time for each option
- Add "Rollback cost" (how hard to change this decision later)

### Example Customization

```markdown
## Decision Priority Levels

Use these guidelines for decision autonomy:

- 🔴 **Critical:** Architecture, folder structure, naming conventions
  → MUST present options and wait for human choice

- 🟡 **Medium:** Code patterns, error handling approaches, test organization
  → Present recommendation with brief trade-offs, proceed if I don't object within 5 minutes

- 🟢 **Low:** Variable names, code formatting, comment style
  → Make autonomous choice following established patterns
```

---

## Lessons from Session

This template emerged from Session experience:

**What Went Wrong:**

- Claude created framework with implicit architectural choices
- Identified only 8 decisions initially, discovered 5 more later
- Made assumptions about drivers/, gateways/, DTOs without discussion
- Required rework and split across multiple sessions

**What This Template Fixes:**

- Forces upfront decision discovery
- Requires presenting all options before implementation
- Documents rationale for choices
- Creates clear checkpoint before work begins

**Result:**

- You make informed decisions with expert guidance
- Zero rework from misaligned assumptions
- Clear audit trail of why choices were made
- Implementation proceeds smoothly after decisions

---

## Template Maintenance

**Location:**

- bare template: `/plan/templates/hitl-decision-template.md`
- usage instructions/versioning: `/plan/templates/hitl-decision-template.README.md` 

**Version:** 1.0 (2025-10-13)

**Update this template when:**

- You discover new anti-patterns to avoid
- You find better ways to present options
- You want to adjust the rigor level
- You learn new lessons from collaborative sessions

---

## Related Documentation

**Process Guide:**

- `/plan/WORKFLOW.md` - Complete collaborative decision-making workflow (start here for full context)

**Related Templates:**

- `/plan/templates/decision-document-template.md` - Decisions artifact that Claude creates during sessions
- `/plan/templates/session-starter-template.md` - For resuming work across sessions (if it exists)

**Other References:**

- `/CLAUDE.md` - Repository overview and default working mode
- `/docs/lean-clean-axioms.md` - Established architectural principles
