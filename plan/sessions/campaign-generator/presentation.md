# Creative Automation for Social Campaigns: 30-Minute Presentation

**FDE Take-Home Exercise**
**Presenter:** [Your Name]
**Date:** [Interview Date]
**Duration:** 30 minutes

> **📝 Note for Implementation:**
> This presentation outline should be updated iteratively as you work through Tasks 1-3.
> Session guides will remind you to update relevant slides as deliverables are completed.

---

## Slide 1: Title & Overview (1 min)

**Title:** Creative Automation Pipeline for Scalable Social Ad Campaigns

**Subtitle:** Enabling hundreds of localized campaigns monthly through AI-driven automation

**Key Points:**
- Client: Global consumer goods company
- Challenge: 100s of campaigns/month, 5 pain points
- Solution: AI-powered creative automation with HITL workflows
- Tech Stack: OpenAI + Claude + Python + Clean Architecture

**Visual:** Hero image from generated campaign (if available)

---

## Slide 2: Business Context & Pain Points (2 min)

**Business Goals:**
1. ⚡ Accelerate campaign velocity
2. 🎨 Ensure brand consistency
3. 🌍 Maximize relevance & personalization
4. 💰 Optimize marketing ROI
5. 📊 Gain actionable insights

**Pain Points:**
1. Manual content creation overload
2. Inconsistent quality & messaging
3. Slow approval cycles (multiple stakeholders)
4. Difficulty analyzing performance at scale
5. Resource drain on creative teams

**Key Insight:** Need automation that maintains brand consistency while accelerating production

---

## Slide 3: Solution Overview (2 min)

**Creative Automation Pipeline:**

```
Campaign Brief (YAML) → Brand Understanding → Asset Generation → Validation → Approval → Launch
                ↓                ↓                  ↓              ↓           ↓
         Claude Vision       OpenAI DALL-E     Legal Checks   HITL Logs   Organized
         Weaviate Search     Text Overlay      Compliance                 Output
```

**Key Features:**
- ✅ Multi-product, multi-locale, multi-aspect ratio generation
- ✅ AI-driven brand understanding (Claude Vision)
- ✅ Intelligent asset reuse (vector similarity)
- ✅ HITL approval workflows (simulated)
- ✅ Monitoring & alerting (agentic system)

**Deliverable:** Working PoC + Architecture + Agentic Design

---

## Slide 4: Architecture Diagram (3 min)

**System Architecture:** Pragmatic Clean Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ DRIVERS (Entry Points)                                       │
│  CLI (Typer) + Streamlit UI                                 │
└────────────────────┬────────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────────┐
│ INTERFACE ADAPTERS                                           │
│  ├─ Orchestrators (Campaign workflow coordination)          │
│  └─ Presenters (Response formatting)                        │
└────────────────────┬────────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────────┐
│ USE CASES (Business Logic)                                   │
│  ├─ Understand Brand                                         │
│  ├─ Generate Campaign                                        │
│  └─ Validate Campaign                                        │
└─────────┬───────────────────┬──────────────────┬────────────┘
          │                   │                  │
┌─────────▼─────────┐ ┌──────▼────────┐ ┌──────▼────────────┐
│ ENTITIES          │ │ ADAPTERS       │ │ INFRASTRUCTURE    │
│  BrandSummary     │ │  AI (OpenAI,   │ │  Repositories     │
│  CampaignBrief    │ │      Claude)   │ │  (YAML, Weaviate) │
│  CreativeAsset    │ │  Storage       │ │                   │
│  Approval         │ │  (Local,MinIO) │ │                   │
│  Alert            │ │  Fake (Demo)   │ │                   │
└───────────────────┘ └────────────────┘ └───────────────────┘
```

**Key Decisions:**
- Clean Architecture for extensibility
- Adapter pattern for AI service substitution
- Dual implementation (Fake + Real) for demo flexibility

**📝 Update:** Add actual architecture diagram from `architecture-diagram.md` when completed

---

## Slide 5: Technology Stack & Integration (2 min)

**Core Technologies:**

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Image Generation** | OpenAI DALL-E 3 | Hero images for campaigns |
| **Text Overlay** | OpenAI API | Campaign message on images |
| **Brand Understanding** | Claude Vision | Analyze brand assets |
| **Localization** | Claude Multilingual | English + Spanish translation |
| **Asset Search** | Weaviate (vector DB) | Intelligent asset reuse |
| **Storage** | MinIO (S3-compatible) | Asset management |
| **UI** | Streamlit | Dashboard for stakeholders |
| **CLI** | Typer (Python) | Automation interface |

**Why These Choices:**
- OpenAI + Claude: Best-in-class AI for images and text
- Weaviate: Semantic search for brand/asset matching
- MinIO: Local S3 simulation, production-ready path
- Python + Clean Architecture: Maintainable, testable, scalable

---

## Slide 6: Implementation Roadmap (2 min)

**Phase 1: Foundation** (Week 1-2)
- ✅ Core entities & domain model
- ✅ Campaign brief schema (YAML)
- ✅ Basic CLI + file structure
- ✅ Fake adapters for testing

**Phase 2: AI Integration** (Week 3-4)
- 🔄 OpenAI image generation
- 🔄 Claude Vision brand analysis
- 🔄 Claude multilingual localization
- 🔄 Text overlay on images

**Phase 3: Intelligence** (Week 5-6)
- 📅 Weaviate integration (asset reuse)
- 📅 Brand similarity search
- 📅 Validation pipeline (legal, compliance)

**Phase 4: Production** (Week 7-8)
- 📅 Streamlit UI for stakeholders
- 📅 MinIO storage integration
- 📅 Logging & monitoring
- 📅 Documentation & handoff

**Phase 5: Agentic System** (Week 9-10)
- 📅 Automated monitoring
- 📅 Alert generation & escalation
- 📅 Model Context Protocol for stakeholder comms

**Legend:** ✅ Complete | 🔄 In Progress | 📅 Planned

**📝 Update:** Refine roadmap with actual sprint breakdown from `roadmap.md`

---

## Slide 7: Demo - Campaign Generation Flow (5 min)

**Live Demo or Recording:**

1. **Input:** Show `briefs/holiday-gift-2025-01.yaml`
   - 2 products (Lavender Soap, Citrus Gel)
   - 3 aspect ratios (1:1, 9:16, 16:9)
   - 2 locales (en-US, es-US)

2. **Execution:** Run CLI command
   ```bash
   python -m drivers.cli generate --brief briefs/holiday-gift-2025-01.yaml
   ```

3. **Process:** Show logs
   - ✅ Brand loaded: natural-suds-co
   - ✅ Generating 12 assets (2×3×2)
   - ✅ OpenAI: 6 hero images generated
   - ✅ Claude: 2 localized slogans
   - ✅ Assets overlaid with text
   - ✅ Validation: 12/12 passed
   - ✅ Approval checkpoint logged
   - ✅ Output saved to `out/assets/`

4. **Output:** Show folder structure
   ```
   out/assets/
   ├─ lavender-soap/
   │  ├─ en-US/ [3 images: 1:1, 9:16, 16:9]
   │  └─ es-US/ [3 images: 1:1, 9:16, 16:9]
   └─ citrus-shower-gel/
      ├─ en-US/ [3 images]
      └─ es-US/ [3 images]
   ```

5. **Visual:** Display 2-3 generated campaign images

**Key Points:**
- End-to-end automation
- Organized output structure
- Localized messaging
- Brand consistent visuals

**📝 Update:** Replace with actual demo screenshots/recordings when available

---

## Slide 8: Agentic System Design (3 min)

**AI-Driven Monitoring & Alerts:**

**Agent Responsibilities:**
1. **Monitor** incoming campaign briefs
2. **Trigger** automated generation tasks
3. **Track** creative variant count/diversity
4. **Flag** missing/insufficient assets
5. **Alert** stakeholders with contextual information

**Alert Types:**
- ⚠️ Missing assets (< 3 variants)
- ⚠️ Generation failures (API errors)
- ⚠️ Validation failures (legal/compliance)
- ⚠️ Approval timeouts (HITL bottlenecks)

**Model Context Protocol:**
```python
context = {
    "brief_id": "holiday-2025-01",
    "issue": "insufficient_variants",
    "expected": 12,
    "actual": 8,
    "missing": ["lavender-soap/es-US/9x16", ...],
    "reason": "OpenAI API rate limit exceeded"
}

# LLM generates stakeholder-friendly message:
"The Holiday Gift campaign is experiencing delays. We've generated
8 of 12 required assets. Missing 4 Spanish story-format images for
Lavender Soap due to API rate limiting. Expected resolution: 2 hours.
Recommend: Approve English assets now, Spanish assets separately."
```

**Stakeholder Communication:** Sample email showing delay explanation

**📝 Update:** Add actual alert examples and email template from Task 3 work

---

## Slide 9: Key Design Decisions & Trade-offs (3 min)

**Decision 1: Pragmatic Clean Architecture**
- ✅ Pro: Extensible, testable, maintainable
- ❌ Con: More upfront structure than script
- **Why:** Interview context + future scaling requirements

**Decision 2: Dual AI Implementation (Fake + Real)**
- ✅ Pro: Demo without API keys, shows adapter pattern
- ❌ Con: More code to maintain
- **Why:** Accessibility for interviewers + design demonstration

**Decision 3: YAML for Campaign Briefs**
- ✅ Pro: Human-readable, comment-friendly, methodology alignment
- ❌ Con: Requires parser library
- **Why:** Stakeholder usability + Lean-Clean YAML-driven approach

**Decision 4: Campaign-Level Localization**
- ✅ Pro: "One campaign, many locales" natural model
- ❌ Con: Product names not localized
- **Why:** Matches PDF requirement, simpler implementation

**Decision 5: Simulated HITL (Logged, Not Interactive)**
- ✅ Pro: Automatable demo, foundation for Task 3
- ❌ Con: Not realistic workflow
- **Why:** Time constraint + Task 3 design focus

**Key Insight:** Decisions optimized for interview context (demonstrate skill) while maintaining production evolution path

---

## Slide 10: Next Steps & Q&A (5 min)

**What Was Delivered:**
✅ Working PoC (GitHub repository)
✅ Architecture diagram & roadmap
✅ Agentic system design
✅ Stakeholder communication template
✅ Comprehensive documentation

**Production Roadmap:**
1. **Immediate (Week 1-2):**
   - Replace fake adapters with production AI integrations
   - Add Streamlit UI for stakeholder approvals
   - Implement real Weaviate asset search

2. **Short-term (Month 1-2):**
   - Brand compliance checks (logo detection, color validation)
   - Performance tracking dashboard
   - A/B testing framework for campaign effectiveness

3. **Long-term (Quarter 1-2):**
   - Multi-brand support
   - Campaign performance optimization (ML-driven)
   - Integration with ad platforms (Facebook, Instagram, TikTok APIs)
   - Advanced agentic workflows (auto-remediation, predictive alerting)

**Key Metrics to Track:**
- Campaign velocity: Briefs/month → Assets/month
- Brand consistency: Compliance score
- ROI: Cost per asset vs. performance (CTR, conversions)
- Stakeholder satisfaction: Approval cycle time

**Questions?**
- Technical implementation details
- Architectural decisions & rationale
- Production scaling considerations
- Integration with existing systems

**Thank You!**
- GitHub: [repository-url]
- Demo Recording: [link]
- Contact: [email]

---

## Appendix: Slide Notes for Presenter

### Timing Breakdown
- Slides 1-3: Context (5 min)
- Slides 4-6: Architecture & Tech (7 min)
- Slide 7: Demo (5 min)
- Slides 8-9: Design Deep-Dive (6 min)
- Slide 10: Wrap-up & Q&A (7 min)

### Key Messages
1. **Understanding:** Deep grasp of business problem and stakeholder needs
2. **Architecture:** Clean, extensible, production-ready design
3. **Implementation:** Working PoC demonstrating core capabilities
4. **Thinking:** Thoughtful trade-offs, appropriate for 6-8 hour constraint
5. **Vision:** Clear path from PoC to production system

### Questions to Anticipate
- Q: Why Clean Architecture for a PoC?
  - A: Interview context (demonstrate design skill) + production evolution path

- Q: Why not use [other GenAI provider]?
  - A: OpenAI + Claude chosen for best-in-class capabilities and complementary strengths

- Q: How would you scale this to 1000s of campaigns?
  - A: Batch processing, queue-based architecture, distributed storage, caching

- Q: What about campaign performance measurement?
  - A: Task 3 agentic system foundation + future dashboard integration

- Q: Real-world deployment challenges?
  - A: API rate limits, cost management, brand approval workflows, compliance

### Visual Assets Needed
- [ ] Architecture diagram (clean, professional)
- [ ] Demo screenshots (2-3 generated campaign images)
- [ ] Folder structure screenshot
- [ ] Alert/email mockup (Task 3)
- [ ] Roadmap Gantt chart or timeline

### Demo Preparation Checklist
- [ ] Test CLI commands work smoothly
- [ ] Prepare example campaign brief YAML
- [ ] Pre-generate some assets if demo fails
- [ ] Have backup slides if live demo issues
- [ ] Time the demo (should be < 5 minutes)

---

**📝 Iterative Update Instructions:**

**Session 01 (Architecture & Roadmap):**
- Update Slide 4 with actual architecture diagram
- Update Slide 6 with detailed roadmap from `roadmap.md`

**Session 02 (PoC Implementation):**
- Update Slide 7 with actual demo screenshots
- Update Slide 9 with implementation learnings
- Add any new trade-offs discovered during coding

**Session 03 (Agentic System):**
- Update Slide 8 with actual alert examples
- Add sample stakeholder email template
- Update Slide 10 with Task 3 deliverables

**Final Polish:**
- Add visual assets (diagrams, screenshots)
- Time each section
- Practice transitions
- Prepare for anticipated questions
