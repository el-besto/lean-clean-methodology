# Agent Understanding - Campaign Generator Project

**Created:** 2025-10-14
**Updated:** 2025-10-14 11:35am
**Status:** Complete Understanding (Post-PDF Review)

## Context: FDE Take-Home Exercise

**Time Budget:** 6-8 hours total
**Final Deliverables:** 30-minute presentation + demo recording
**Client Scenario:** Global consumer goods company launching hundreds of localized social ad campaigns monthly

## Task Overview

### Task 1: High-Level Architecture Diagram and Roadmap
Create architecture and roadmap for creative automation pipeline addressing:
- **Stakeholders:** Creative Lead, Ad Operations, IT, Legal/Compliance
- **System Components:** Asset ingestion, Storage, GenAI-based asset generation
- **Deliverables:**
  - High-Level Architecture Diagram
  - Roadmap (1 slide showing delivery timelines and epics)

### Task 2: Build Creative Automation Pipeline (Proof of Concept)
Working PoC demonstrating automated creative asset generation:
- **Minimum Requirements:**
  - Accept campaign brief (JSON/YAML): products (≥2), target region/market, target audience, campaign message
  - Accept input assets (local folder or mock storage); reuse when available
  - Generate new assets using GenAI when missing
  - Produce creatives for ≥3 aspect ratios (1:1, 9:16, 16:9)
  - Display campaign message on final posts (English minimum, localized bonus)
  - Run locally (CLI or simple app)
  - Save outputs organized by product and aspect ratio
  - README with: how to run, example I/O, design decisions, assumptions/limitations
- **Nice-to-Have (Bonus):**
  - Brand compliance checks (logo presence, brand colors)
  - Legal content checks (prohibited words)
  - Logging/reporting
- **Deliverable:** Public GitHub repository with code + comprehensive README

### Task 3: Agentic System Design & Stakeholder Communication
AI-driven agent design for:
- Monitor incoming campaign briefs
- Trigger automated generation tasks
- Track count and diversity of creative variants
- Flag missing/insufficient assets (< 3 variants)
- Alert and/or logging mechanism
- **Model Context Protocol:** Define information LLM sees for human-readable alerts
- **Deliverables:**
  - Agentic System Design document
  - Sample stakeholder email (e.g., explaining delays due to GenAI API provisioning issues)

## Success Criteria

1. ✅ Session files stored in `plan/sessions/campaign-generator/`
2. ✅ Information ready for "Task 1: Create a High-Level Architecture Diagram and Roadmap"
3. ✅ Draft Python classes for the PoC
4. ✅ List of functions to compose for the use case
5. ✅ YAML file for campaign brief inputs
6. ✅ Ready to work through Task 1 and Task 2 via HITL processes
7. ✅ Minimal 30-minute presentation outline (10 slides) in `presentation.md`

## Key Reference Materials

- `plan/templates/session-template.md` - Session structure template
- `plan/sessions/_session-03a.md` - Steel-thread PoC approach example
- `../lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md` - "TestLocalizedCampaignFeature" inspiration

**Note:** `plan/drafts/` is for methodology documents, not project work. All campaign-generator work goes in `plan/sessions/campaign-generator/`.

## Approach

**Mode:** Direct implementation with decision flagging (not full HITL upfront)
- User has provided substantial ideation already
- Will flag architectural decisions as they arise
- Will structure according to Lean-Clean methodology

**Focus:**
- Input/output parameters and entity properties
- Method stubs (no detailed implementation)
- Pipeline definition for Task 1
- Proper documentation for subsequent HITL processes

## Deliverable File Tree

```
plan/sessions/campaign-generator/
├── _agent-understanding.md                 # This file - complete context
├── _scenario-context.md                    # Extracted requirements from PDF
├── _decisions-needed.md                    # HITL decisions document
├── session-01-architecture-roadmap.md      # Task 1: Architecture & Roadmap
├── session-02-steel-thread-poc.md          # Task 2: Steel Thread PoC
├── entities.md                             # Domain entities
├── use-cases.md                            # Use case definitions with signatures
├── campaign-brief-schema.yaml              # Input schema
├── architecture-diagram.md                 # Textual diagram representation
├── roadmap.md                              # Implementation roadmap
├── presentation.md                         # 10-slide outline (30 min)
└── function-composition.md                 # Pipeline flow and dependencies
```

## Initial Domain Understanding (From Ideation)

### Core Entities

**BrandSummary:**
- `brand_id`
- `campaign_slogans`
- `target_audiences`
- `target_regions`
- `products`
- `colors` (added for brand consistency)
- `description` (added for brand consistency)

**CampaignBrief:**
- `brand_id`
- `target_region`
- `target_audience`
- `campaign_slogan`
- `products: List[Product]`
- `aspects: List[str]`

**Product:**
- `name`
- `locale` (question: belongs here or in CampaignBrief?)
- `palette_words: List[str]`

**CreativeAsset:**
- `brand_id`
- `product_id`
- `audience`
- `message`
- `aspect`
- `image_url`
- `reused: bool`
- `meta: dict`

**Approval:** (HITL process tracking)
- `id`
- `brief_id`
- `status`
- `started_at`
- `finished_at`
- `product`
- `ratio`
- `approved: bool`
- `comment`

**Alert:** (Telemetry/monitoring)
- `id`
- `brief_id`
- `created_at`

### Core Functions (Pipeline)

**Brand Understanding & Consistency:**
1. `understand_brand() -> BrandSummary` - Uses Claude Vision
2. `search_similar_brands(brand_summary, campaign_brief, input_artifacts) -> List[BrandSummary]`
3. `accept_brand_choice() -> BrandSummary` - HITL approval

**Campaign Generation:**
4. `generate_personalized_slogans(brand_summary) -> List[Slogan]` - Multilingual
5. `validate_personalized_slogans(slogans) -> ValidationResult` - Consistency + compliance
6. `localize_campaign_slogans(slogans, locales) -> Dict[str, Slogan]`
7. `add_slogan_to_images(images, slogans) -> List[CreativeAsset]`

**Approval Workflows:**
8. `accept_campaign_images() -> Approval` - HITL approval

### Key Integration Points

- **Claude Vision:** Brand understanding (https://docs.claude.com/en/docs/build-with-claude/vision)
- **Claude Multilingual:** Slogan localization (https://docs.claude.com/en/docs/build-with-claude/multilingual-support)
- **Weaviate:** Vector store for brand/image search
- **Blob Storage:** Creative asset storage
- **Email:** Campaign gallery delivery

### Success Metrics (from ideation)

1. **Brand Consistency:** Colors, description, style coherence
2. **Campaign Relevance:** Multi-lingual, personalized, validated
3. **ROI:** Cost to convert vs cost to produce (requires telemetry)
4. **Optimization:** Appropriate telemetry/events for measurement

## Business Goals & Pain Points (From PDF)

### Business Goals
1. **Accelerate campaign velocity:** Rapidly ideate, produce, approve, and launch more campaigns per month
2. **Ensure brand consistency:** Maintain global brand guidelines and voice across all markets and languages
3. **Maximize relevance & personalization:** Adapt messaging, offers, and creative to local cultures, trends, preferences
4. **Optimize marketing ROI:** Improve CTR/conversions versus cost and efficiencies (time + spend)
5. **Gain actionable insights:** Track effectiveness at scale; learn what content/creative/localization drives outcomes

### Pain Points
1. **Manual content creation overload:** Creating/localizing variants for hundreds of campaigns is slow, expensive, error-prone
2. **Inconsistent quality & messaging:** Risk of off-brand or low-quality creative due to decentralized processes
3. **Slow approval cycles:** Bottlenecks in review/approval with multiple stakeholders per region/market
4. **Difficulty analyzing performance at scale:** Siloed data and manual reporting hinder learning/optimization
5. **Resource drain:** Skilled creative/marketing teams overloaded with repetitive requests vs value-driving work

## Mapping to Lean-Clean Methodology

### Phase Alignment

**This exercise maps to:**
- **P0 (Context Framing):** Business goals + pain points define the "why"
- **P2 (Ideation & Concept Design):** Task 1 - Architecture Diagram + Roadmap
- **P3 (Refinement & Steel Thread):** Task 2 - PoC with minimal viable slice
- **P8 (Agentic System Design):** Task 3 - Monitoring, validation, alerts

**Steel Thread Scope (Task 2):**
- Single campaign brief input (YAML)
- ≥2 products
- ≥3 aspect ratios (1:1, 9:16, 16:9)
- Asset reuse OR GenAI generation
- Campaign message overlay (English minimum)
- Local output folder organized by product/aspect ratio

### PoC Type Decision

**Recommendation: Pragmatic CA (not Steel Thread)**

**Rationale:**
- Task 3 requires agentic monitoring/alerting → needs Infrastructure layer for observability
- Multiple stakeholders (Creative Lead, Ad Ops, IT, Legal) → needs clear boundaries
- Brand compliance + legal checks → needs validation layer
- "Nice-to-have" features suggest evolution path → needs substitutable adapters

**However, for 6-8 hour constraint:**
- Use Pragmatic CA structure
- Implement Steel Thread feature set
- Document expansion path for Full CA

## Open Questions (To Be Resolved via HITL)

### Architectural Decisions
1. **PoC Structure:** Steel Thread vs Pragmatic CA? (Leaning Pragmatic CA)
2. **Storage Strategy:** Local filesystem vs mock cloud (S3/Azure)? (Suggest local with S3 adapter interface)
3. **GenAI Provider:** Which image generation API? (Suggest DALL-E 3 via OpenAI)
4. **Brand Store:** How to represent brand guidelines? (Entity vs external file?)
5. **Asset Reuse Logic:** Simple filename match vs vector similarity? (Suggest filename for PoC)

### Domain Model Questions
6. Does `locale` belong in `Product` or `CampaignBrief`? (Suggest CampaignBrief for multi-locale campaigns)
7. What constitutes "input_artifacts" for brand search? (Brand guidelines doc + reference images)
8. What validation rules in `validate_personalized_slogans`? (Length, prohibited words, brand voice)
9. What compliance checks are required? (Prohibited words list, required disclaimers)
10. How do we define "similar brands" in vector search? (MVP: exact match by brand_id)

### Implementation Scope
11. **Localization:** English-only or multi-locale for PoC? (Suggest English + 1 locale for bonus)
12. **HITL Approval:** In PoC or documented for Task 3? (Document for Task 3, simulate in PoC)
13. **Telemetry:** What events to emit? (Brief received, asset generated, validation failed, output saved)

## Next Steps

1. ✅ Read PDF and update understanding
2. ⏭️ Create decisions document (`_decisions-needed.md`)
3. ⏭️ Create session files for Task 1 and Task 2
4. ⏭️ Define entities, use cases, and schemas
5. ⏭️ Create architecture diagram (textual representation)
6. ⏭️ Create roadmap
7. ⏭️ Create presentation outline (10 slides)
