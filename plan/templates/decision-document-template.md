# [Task Name]: Decisions Needed

> **Template Version:** 1.0 (2025-10-13)
>
> **Usage Instructions:** See `/plan/templates/decision-document-template.README.md` for how to use this template, when to create it, and how it fits into the collaborative decision-making workflow.
>
> **Related Template:** This document is created by Claude during the decision discovery phase, after you provide a task using `/plan/templates/hitl-decision-template.md`.

---

**Status:** 🚧 0 of 0 Resolved | 0 Awaiting Discussion

This document captures the key architectural decisions for [brief description of task].

**Update:** [Add notes here as new decisions are discovered during analysis]

---

## Decision 1: [Decision Name] ⚠️ [Add CRITICAL flag if applicable]

**What I did:**
- [Describe approach taken, if any work was done before decision was identified]
- [If this is upfront decision discovery, replace this section with "Not yet started - decision identified during analysis"]

**Context:**
[Relevant background - what have we done before? What do docs say? What did analysis reveal?]

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
  - ✅ Pro: [Benefit]
  - ❌ Con: [Drawback]
  - ❌ Con: [Drawback]

- [ ] **Option C: [Name]** ([brief descriptor])
  - [Brief description of this approach]
  - ✅ Pro: [Benefit]
  - ❌ Con: [Drawback]

**My recommendation:** Option [A/B/C]

**Reasoning:**
[Why I think this option best fits the requirements/context - make clear this is a suggestion, not a choice]

**Your decision:** [AWAITING INPUT] or ✅ **Option X: [Name]**

**Rationale:**
[Space for you to fill in after deciding - why you chose this option]

**Impact:** [CRITICAL/HIGH/MEDIUM/LOW] - [Brief description of what this affects]

**Status:** 🚧 AWAITING or ✅ RESOLVED

---

## Decision N: [Decision Name]

**For additional decisions, copy the structure from Decision 1 above.**

Each decision should include:
- What I did (context about prior work, if any)
- Context (background from docs/analysis)
- Question for you (clear framing)
- Options with checkboxes, pros/cons
- My recommendation with reasoning
- Space for your decision and rationale
- Impact assessment
- Status (🚧 AWAITING or ✅ RESOLVED)

---

## Summary of Decisions

| # | Decision | Priority | Status |
|---|----------|----------|--------|
| 1 | [Decision Name] | [CRITICAL/HIGH/MEDIUM/LOW] | [🚧 AWAITING / ✅ RESOLVED] |
| 2 | [Decision Name] | [Priority] | [Status] |
| N | [Add rows as needed] | [Priority] | [Status] |

**Priority Levels:**
- ⚠️ **CRITICAL**: Affects fundamental architecture, blocking, or has cascading impact
- **HIGH**: Significant impact on implementation, affects multiple components
- **MEDIUM**: Important but localized impact
- **LOW**: Minor impact, easily changed later

---

## Next Steps

**Completed:**
- ✅ [List steps already completed]
- ✅ [E.g., "Analyzed requirements and identified architectural decisions"]
- ✅ [E.g., "Reviewed relevant documentation"]

**Awaiting Your Input ([X] decisions):**
1. **Decision X: [Name]** ⚠️ [Flag if CRITICAL or HIGH priority]
2. **Decision Y: [Name]**
3. **Decision Z: [Name]**

**After Decisions Resolved:**
1. [What happens next - e.g., "Update framework document with resolved folder structures"]
2. [E.g., "Create code examples following decided patterns"]
3. [E.g., "Update related documentation for consistency"]
4. [E.g., "Commit all work with summary of decisions made"]

**Status:** [X] of [Y] decisions resolved | [Z] awaiting collaborative review

---

## Notes

**Decision Discovery Process:**
- [Document any insights about how decisions were identified]
- [Note if additional decisions were discovered after initial analysis]
- [Track any dependencies between decisions]

**Related Documents:**
- [Link to analysis docs that informed these decisions]
- [Link to prior decision documents if this builds on previous work]
- [Link to axioms or architectural principles being applied]
