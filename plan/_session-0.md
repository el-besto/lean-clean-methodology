# Session 0: Base Context

**Usage Note:** This file provides foundational context and should be **concatenated at the beginning of every session** (01, 02, 03, 04a, 04b, 04c). Each new Claude Code session starts fresh with no memory, so Session 0 ensures consistent grounding across all methodology work.

**How to use:**
```bash
# For any session, concatenate Session 0 first:
cat plan/_session-0.md plan/_session-01.md > combined-session-01.md
cat plan/_session-0.md plan/_session-02.md > combined-session-02.md
# etc.
```
  
## Background:

### How to implement a Methodology

Methodology (highest/broadest)
   ↓
Framework
   ↓
Practices/Guidelines (style guides, standards, etc.)
   ↓
Tools/Templates (project seeds, boilerplates, etc.)

#### The reasoning:

- **Methodology** defines the philosophical approach and high-level process - the "what" and "why" of how you work (e.g., Agile, Waterfall, DevOps)
- **Framework** provides the structure and concrete mechanisms for implementing that methodology - the "how" (e.g., Scrum framework implements Agile methodology, SAFe framework implements scaled Agile)
- **Practices** are specific techniques within the framework
- **Tools** are the actual artifacts you use

### Diagrams that inspired me

#### Clean Architecture diagrams

Relevant for the Framework decisions (especially folder layout and test structure):

![clean-architecture-diagram-nikolovlazar](/Users/ac/Code/el-besto/lean-clean-methodology/images/ca/clean-architecture-diagram-nikolovlazar.jpg) - modernizing view for modern web apps

![clean-architecture-diagram-martin](/Users/ac/Code/el-besto/lean-clean-methodology/images/ca/clean-architecture-diagram-martin.jpg) - original diagram

#### Lean Product Playbook

Helps us stay "customer focused" whether customer is the enterprise stakeholders, or the end users. Also, may influence naming conventions used for TDD/BDD paradigms/idoms in the Framework, Practice, and Tools/Templates steps.

![lean-product-playbook-pyramid](/Users/ac/Code/el-besto/lean-clean-methodology/images/lpp/lpp-pyramid-product-market-fit.png)

The fundamental model showing the relationship between Product (Solution Space) and Market (Problem Space):

Structure:

**Product (Top 3 Layers - Orange) = Solution Space**
- **5. UX** (Top layer) - The user experience and interface
- **4. Feature Set** - The functionality/capabilities that deliver benefits
- **3. Value Proposition** - The set of needs you aim to meet

**Market (Bottom 2 Layers - Blue) = Problem Space**
- **2. Underserved Needs** - Customer needs not adequately met by competitors
- **1. Target Customer** (Base layer) - The specific customer segment you're targeting

**Product-Market Fit:** Represented by bidirectional arrows between the Product and Market sections. This is the measure of how well your product (top 3 layers) satisfies the market (bottom 2 layers).

**Key Insight:** This diagram clarifies that the top 3 layers are "Solution Space" (what you build) and the bottom 2 layers are "Problem Space" (customer needs). Product-market fit is achieved when your solutions effectively address underserved customer needs.

---

![The Lean Product Process](/Users/ac/Code/el-besto/lean-clean-methodology/images/lpp/lpp-pyramid-the-process.png) - The Lean Product Process.
The Product-Market Fit Pyramid consists of 5 layers divided into two sections:

Market (Bottom Section - Blue):
1. **Target Customer** (Base layer)
2. **Underserved Needs** (Second layer)

Product (Top Section - Orange):
1. **Value Proposition** (Third layer)
2. **Feature Set** (Fourth layer)
3. **UX** (Top layer)

**Product-Market Fit:** The measure of how well the Product layers satisfy the Market layers, with bidirectional arrows showing the relationship.

The 6-Step Process:
1. Determine your target customers
2. Identify underserved customer needs
3. Define your value proposition
4. Specify your minimum viable product (MVP) feature set
5. Create your MVP prototype
6. Test your MVP with customers (shown as iterative loop back to step 1)


#### Turning "Desirements" into Acceptance Criteria
4c's during workshoping, use case definition, acceptance criteria, etc.

![Cards -> Conversation -> Confirmation -> Context](/Users/ac/Code/el-besto/lean-clean-methodology/images/acceptance-criteria/cards-conversation-confirmation-context.png)

**Four components of user story development:**

1. **Context (Validation)**
   - Epic Name, Story Name
   - Shows example of "Donate Items" feature with categories (Furniture, Electronics, Clothes, Toys, Other)

2. **Card**
   - Yellow sticky note format
   - "As a <...> I want <...> so that <...>"
   - The written user story

3. **Conversation**
   - Discussions between stakeholders to agree on the story details
   - The dialogue that clarifies requirements

4. **Confirmation (Verification)**
   - Acceptance Criteria
   - Given <...> When <...> Then <...>
   - Checkmark indicates verified

**given-when-then-example**

![Example Given-When-Then](/Users/ac/Code/el-besto/lean-clean-methodology/images/acceptance-criteria/given-when-then-acceptance-criteria.webp)


## Lean-Clean Methodology

Goal: to accelerate PoCs in the enterprise context. We want to have several interleaved artifacts as part of a larger framework - it will be called "Lean-Clean Methodology". The primary usage will be helping to plan/execute on PoC’s that leverage Gen-AI technologies to automate, accelerate, and where appropriate innovate, complex enterprise workflows. Its secret sauce is to define executable tests as early as possible with core stakeholders to model out complex enterprise processes and leverage the executable tests to get sign-off before time-intensive implementation begins.  Moreover, the tests themselves will be human-readable so that (in theory) the core stakeholders could learn how to draft them and own them. That is why the code and abstractions must be written for these tests must use DDD/CA/BDD-style concepts/syntax to encapsulate the "what" and the "when" and leave the "how" for the implementation experts.

### Primary Components
The primary components of the Lean-Clean Methodology are as follows (Note: they have not been broken into the suggested structure from Background):

A. Methodological definition of phases (name, core question, purpose, key output, key artifacts). Each phase will have a clear Goal, list of activities, and expected business inputs and outputs.

B. A clear definition of the software development approach which leverage elements of Clean Architecture, the Lean Product Playbook methodology, Domain Driven Design, TDD and BDD.  The definition will include (a) folder architecture, (b) how to define executable tests as early as possible with core stakeholders, and leverage the executable tests to get sign-off before time-intensive implementation begins. (c) what PoC-types ("Base Structures") the framework supports (eg Steel Thread, Pragmatic CA, Full CA). Each Base Structure should be able to evolve from one to the next because they will all use the same underlying Python and "Lean-Clean Code Paradigms".

C. An "In-Practice" Review Guide/Workbook to guide a team on how to (a) select which type of PoC is right for the current situation, and (b) understand how the methodology can be applied.

D. Lean-Clean Code Paradigms: A living Python Patterns document which explicitly demonstrates the suggested coding patterns to use for each of the PoC-types ("Base Structures").

### The Secret Sauce: Outside-In with All Stakeholders
The key differentiator of this methodology is writing realistic, executable test code with all stakeholders present before implementing any expensive infrastructure. This isn't just developer TDD—it's multi-stakeholder specification by example.

1. **Write orchestrator tests with stakeholders** → Executable specifications
2. **Write use case tests with domain experts** → Business rules documented
3. **Implement with fakes** → Test without expensive external dependencies
4. **Implement real adapters** → Parallel development possible


---

### Previous Work (For Reference)
I've already drafted:
- Blog post content exploring multi-stakeholder workshops [The Secret Sauce: Outside-In with All Stakeholders](/Users/ac/Code/el-besto/lean-clean-blog/wip/_wip-lean-clean-the-secret-sauce.md)
- Example tests showing the "Outside-In" approach

These are helpful context but should not constrain your thinking.

