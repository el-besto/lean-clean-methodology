### Your Role in This Session
You are my methodology design partner. I need you to:
- Challenge assumptions that don't serve the goal
- Point out conflicts or gaps in my thinking
- Suggest pragmatic solutions (favor practicality over purity)
- Ask clarifying questions when my intent is unclear

### What Success Looks Like
By the end of this session, I should have:
- A clear, written definition of 3-5 methodology phases
- Confidence that we've resolved the major architectural questions
- A shared vocabulary for discussing the Framework (next session)
- Clarity on which parts of CA/DDD/BDD we're adopting vs. adapting

## Claude Instructions

### Context
I've been sketching out a methodology called "Lean-Clean" to accelerate enterprise PoCs using Gen-AI. I have three WIP projects that contain early explorations, but they're not definitive—they're just grounding material.

**Important Guidance for This Session:**
Previous explorations generated valuable material but tended to jump too quickly into implementation details without first solidifying the conceptual foundation. In this session:
- **Focus on methodology definition first** (phases, principles, goals) before implementation
- **Resolve architectural questions** before documenting folder structures
- **Extract patterns** from the sketch projects rather than treating them as requirements
- **Challenge assumptions** rather than accepting existing work at face value

The sketch projects (lean-clean-core-skeleton, calibration-service, creative-ai-poc) should inspire you, not constrain you.

### Your Task for This Session

**Help me refine and define the Lean-Clean Methodology itself.** (Component A).

Specifically:
1. **Review my sketch projects** (I'll point you to them)
   - I have three WIP projects: `lean-clean-core-skeleton`, `calibration-service`, `creative-ai-poc`
   - Located at:
     - /Users/ac/Code/el-besto/lean-clean-code/lean-clean-core-skeleton
     - /Users/ac/Code/el-besto/creative-ai-poc
     - /Users/ac/Code/el-besto/calibration-service
   - Extract what's working, identify conflicts
   - Prior analysis is available at: /Users/ac/Code/el-besto/lean-clean-code/CLEAN_ARCHITECTURE_ANALYSIS.md

2. **Review my existing phase definitions**
   - located in README.lean-clean.md for steps 3 and 4. For step 4, I dont believe we will need to redefine much if anything.

3. **Answer these divergent questions** that are blocking methodology definition:
   - How much Clean Architecture should PoCs adopt? (Full CA vs Pragmatic subset?)
   - Where do cross-cutting concerns (events, metrics, observability) live in the architecture?
   - What's the right balance between pytest patterns and BDD/Gherkin syntax?
   - What's the balance between pytest and BDD/Gherkin?
   - Should we have a concept above "Orchestrator" (like "Feature")?
   - How do Steel Thread, Pragmatic CA, and Full CA relate/evolve?

4. **Define the methodology phases** (Component A from Primary Components)
   - 3-5 phases with clear: Name, Core Question, Purpose, Key Output
   - For each phase: Goal, Activities, Expected Business Inputs/Outputs
   - Use the Lean Product Playbook pyramid as inspiration for structure

### Constraints
- Assume multi-stakeholder enterprise PoCs (Creative Lead, Ad Ops, IT, Legal/Compliance)
- Use TDD/BDD principles to turn "desirements" into executable code
- All examples should center on: A multi-agent chat app helping creatives generate social media campaign images
- Don't worry about small "spike" PoCs yet (we'll cover those later)

### Success Criteria for This Session
- [ ] Divergent questions answered with rationale
- [ ] Methodology phases defined and documented
- [ ] Foundation laid for Framework definition (next session)


**Note:** We'll tackle Framework definitions (Component B), folder structure, and Python examples in follow-up sessions.