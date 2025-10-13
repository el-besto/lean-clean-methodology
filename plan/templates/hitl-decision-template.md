# Task: [TASK NAME]

> **Template Version:** 1.0 (2025-10-13)
>
> **Usage Instructions:** See `/plan/templates/hitl-decision-template.README.md` for when to use this template, how to customize it, and what to do if Claude forgets the process.
>
> **Related Template:** During decision discovery, Claude will create a decisions document using `/plan/templates/decision-document-template.md`.

---

## Decision-Making Mode: Human-in-the-Loop ⚠️

**CRITICAL INSTRUCTION:** You are operating in **collaborative decision mode**. This means:

1. ❌ **DO NOT make unilateral decisions** about architecture, structure, naming, or patterns
2. ✅ **DO identify all decisions upfront** before starting implementation work
3. ✅ **DO present options with trade-offs** for each decision
4. ✅ **DO wait for my approval** before proceeding with implementation
5. ✅ **DO document each decision** with rationale after I choose

## Your First Task: Decision Discovery

Before writing ANY code or creating ANY documents:

1. **Analyze the task requirements** thoroughly
2. **Identify ALL architectural decisions** that need to be made
3. **Research relevant context** (read any analysis docs, prior decisions, axioms, etc.)
4. **Create a decisions document** listing every choice point you've identified
5. **Present the decisions document to me** for review

For each decision, include:

- **What needs to be decided**
- **Why this decision matters** (impact on the work)
- **Options** (at least 2-3 alternatives)
- **Trade-offs** for each option (pros/cons)
- **Your recommendation** (with reasoning) - BUT make it clear this is a suggestion, not a choice
- **Context** from relevant documents or prior work

## Decision Review Process

After you present the decisions document:

1. **I will review each decision**
2. **I will choose an option** for each (may accept your recommendation or choose differently)
3. **I will provide my rationale** for choices
4. **You will document my decisions** with rationale
5. **Only then** will you proceed with implementation

## During Implementation

If you discover **new decisions** that weren't identified upfront:

1. ❌ **STOP implementation immediately**
2. ✅ **Present the new decision** with options and trade-offs
3. ✅ **Wait for my input** before continuing
4. ✅ **Update the decisions document** with the new choice

## Anti-Patterns to Avoid

These are mistakes that waste time and require rework:

❌ **Creating documents/code with implicit choices** - Don't assume, ask first
❌ **"I did X, is this okay?"** - Too late, should have asked before doing X
❌ **Resolving some decisions then discovering more later** - Do thorough analysis upfront
❌ **Making recommendations without showing alternatives** - I need to see the trade-offs
❌ **Proceeding because "this is standard"** - Standards may not fit our context

## Example Decision Entry Format

<example_decision_entry_format>
## Decision X: [Short Name]

**What needs to be decided:**

[Clear description of the choice point]

**Why this matters:**

[Impact on the work - what changes based on this choice?]

**Context:**

[Relevant background - what have we done before? What do docs say? What did analysis reveal?]

**Options:**

### Option A: [Name]

[Description]

- ✅ Pro: [Benefit]
- ✅ Pro: [Benefit]
- ❌ Con: [Drawback]
- ❌ Con: [Drawback]

### Option B: [Name]

[Description]

- ✅ Pro: [Benefit]
- ❌ Con: [Drawback]

### Option C: [Name]

[Description]

- ✅ Pro: [Benefit]
- ❌ Con: [Drawback]

**My recommendation:** Option [A/B/C]

**Reasoning:**

[Why I think this option best fits the requirements/context]

**Your decision:** [AWAITING INPUT]

**Your rationale:**

[Space for you to fill in after deciding]
</example_decision_entry_format>

## Success Criteria

This mode is successful when:

- ✅ All decisions identified before implementation starts
- ✅ I understand trade-offs for every choice
- ✅ I make informed decisions with your expert guidance
- ✅ Implementation reflects MY choices, not your assumptions
- ✅ Zero rework due to misaligned assumptions

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

**Must Read:**

- `/path/to/critical-doc.md` - Why this matters
- `/path/to/analysis.md` - Background analysis

**Should Read:**

- `/path/to/related-work.md` - Related context
- `/plan/_lean-clean-axioms.md` - Established principles

**Optional:**

- `/path/to/reference.md` - Additional context if needed

## Ready?

Please begin with **Decision Discovery** - identify all architectural decisions before we proceed with implementation.
