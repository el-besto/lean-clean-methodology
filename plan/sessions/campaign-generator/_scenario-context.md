# Campaign Generator: Scenario Context

**Source:** FDE Take-Home Exercise
**Date:** 2025-10-14
**Time Budget:** 6-8 hours total (across all 3 tasks)
**Final Deliverable:** 30-minute presentation + demo recording

---

## Client Overview

**Client:** Global consumer goods company

**Scale:** Launching hundreds of localized social ad campaigns monthly

**Challenge:** Manual creative production is slow, expensive, error-prone, and doesn't scale

---

## Business Goals

### 1. Accelerate Campaign Velocity
Rapidly ideate, produce, approve, and launch more campaigns per month to drive localized engagement and conversion.

### 2. Ensure Brand Consistency
Maintain global brand guidelines and voice across all markets and languages.

### 3. Maximize Relevance & Personalization
Adapt messaging, offers, and creative to resonate with local cultures, trends, and consumer preferences.

### 4. Optimize Marketing ROI
Increase campaign efficiency by improving performance and top-line growth (CTR, conversions) versus cost and efficiencies (both time and spend).

### 5. Gain Actionable Insights
Track effectiveness at scale and learn what content/creative/localization drives the best business outcomes.

---

## Pain Points

### 1. Manual Content Creation Overload
Creating and localizing variants for hundreds of campaigns per month is slow, expensive, and error-prone.

### 2. Inconsistent Quality & Messaging
Risk of off-brand or low-quality creative due to decentralized processes and agencies.

### 3. Slow Approval Cycles
Bottlenecks in review/approval with multiple stakeholders in each region and market.

### 4. Difficulty Analyzing Performance at Scale
Siloed data and manual reporting hinder learning and optimization.

### 5. Resource Drain
Skilled creative and marketing teams are overloaded with repetitive requests versus value-driving work.

---

## Objective

Design a **creative automation pipeline** that enables the creative team to generate variations for campaign assets.

---

## Stakeholders

1. **Creative Lead** - Owns brand consistency and creative quality
2. **Ad Operations** - Manages campaign execution and scaling
3. **IT** - Provides infrastructure and integration support
4. **Legal/Compliance** - Ensures regulatory compliance and brand safety

---

## Data Sources

### User Inputs
Campaign briefs and assets uploaded manually.

### Storage
Storage to save generated or transient assets (Can be Azure, AWS, or Dropbox).

### GenAI
Best-fit APIs available for generating hero images, resized and localized variations.

---

## Task Breakdown

### Task 1: Create a High-Level Architecture Diagram and Roadmap

**Deliverables:**
- **Architecture Diagram:** Detailed architecture diagram for the overall content pipeline
- **Roadmap:** High-level roadmap (1 slide) showing overall delivery timelines and epics

**System Architecture Components:**
- Asset ingestion
- Storage
- GenAI-based asset generation

**Presentation Context:**
Include in 30-minute presentation covering:
- High-level Roadmap and Approach
- Backend and data integration design

---

### Task 2: Build a Creative Automation Pipeline (Proof of Concept)

**Goal:**
Demonstrate a working proof-of-concept that automates creative asset generation for social ad campaigns using GenAI. The implementation should show technical approach, problem-solving, and ability to integrate creative technologies.

#### Minimum Requirements

**Input:**
- Accept a **campaign brief** (in JSON, YAML, or other reasonable format) with:
  - Product(s) – at least **two different products**
  - Target region/market
  - Target audience
  - Campaign message
- Accept **input assets** (can be in a local folder or mock storage) and reuse them when available

**Processing:**
- When assets are missing, generate new ones using a GenAI image model
- Produce creatives for **at least three aspect ratios** (e.g., 1:1, 9:16, 16:9)
- Display **campaign message** on the final campaign posts (English at least, localized is a plus)

**Execution:**
- Run **locally** (command-line tool or simple local app; your choice of language/framework)

**Output:**
- Save generated outputs to a folder, clearly organized by **product and aspect ratio**

**Documentation:**
- Include basic documentation (README) explaining:
  - How to run it
  - Example input and output
  - Key design decisions
  - Any assumptions or limitations

#### Nice to Have (Optional for Bonus Points)

- **Brand compliance checks** (e.g., presence of logo, use of brand colors)
- **Simple legal content checks** (e.g., flagging prohibited words)
- **Logging or reporting** of results

#### Evaluation Criteria

"Please ensure that your solution reflects thoughtful design choices and demonstrates a clear understanding of the code. These aspects will be part of the evaluation."

**Deliverable:**
A public GitHub repository containing:
- The creative automation pipeline code
- A comprehensive README file

---

### Task 3: Create an Agentic System Design & Stakeholder Communication

**Goal:**
Design an AI-driven agent to support campaign workflow automation and stakeholder communication.

#### Agent Responsibilities

- **Monitor** incoming campaign briefs
- **Trigger** automated generation tasks
- **Track** the count and diversity of creative variants
- **Flag** missing or insufficient assets (e.g., fewer than 3 variants)
- **Alert and/or Logging** mechanism

#### Model Context Protocol

Define the information the LLM sees to draft human-readable alerts.

#### Sample Stakeholder Communication

Compose an email to customer leadership explaining delays (e.g., due to GenAI API provisioning or licensing issues).

**Deliverables:**
- Agentic System Design document
- Email - Stakeholder Communication sample

---

## Success Metrics (Implicit from Business Goals)

### Brand Consistency
- Colors match brand guidelines
- Description and style are coherent
- Voice is consistent across locales

### Campaign Relevance
- Multi-lingual support (English + locales)
- Personalized messaging
- Validation passes (legal, compliance)

### ROI
- Cost to convert vs. cost to produce (requires telemetry)
- Faster campaign velocity metrics

### Optimization
- Appropriate telemetry/events for measurement
- Performance tracking at scale
- Learnings captured and actionable

---

## Technical Constraints & Preferences

### Must Run Locally
- Command-line tool or simple local app
- Interviewers need to be able to run it easily

### Storage Options
- Local folder (simplest)
- Mock storage (Azure, AWS, Dropbox)
- Must be clearly organized

### GenAI APIs
- "Best-fit APIs available"
- For hero image generation
- For resizing and localized variations

### Language/Framework
- "Your choice"
- Must include comprehensive README

---

## Time Management

**Total Budget:** 6-8 hours across all three tasks

**Task 1:** Architecture + Roadmap (planning/design)
**Task 2:** Working PoC (implementation)
**Task 3:** Agentic Design + Communication (design/documentation)

**Suggested Allocation:**
- Task 1: 1-1.5 hours (architecture diagram + 1-slide roadmap)
- Task 2: 4-5 hours (core PoC implementation + README)
- Task 3: 1-1.5 hours (agent design + email sample)
- Presentation prep: 30-60 minutes

**Note:** Required to record a quick demo to help interviewers set up/run the app locally for review.

---

## Deliverable Summary

1. **Presentation** (30 minutes)
   - High-level Roadmap and Approach
   - Backend and data integration design
   - Demo recording

2. **GitHub Repository**
   - Creative automation pipeline code
   - Comprehensive README

3. **Agentic System Design**
   - Agent design document
   - Stakeholder email sample

**Submission:** Send presentation and recording to Talent Partner the day before scheduled interview.

---

## Key Insights for Implementation

### What They're Looking For

1. **Technical Approach** - How you architect the solution
2. **Problem-Solving** - How you handle constraints and make trade-offs
3. **Integration Skills** - Ability to integrate creative technologies (GenAI)
4. **Design Choices** - Thoughtful decisions with clear rationale
5. **Code Understanding** - Demonstrable comprehension of implementation

### What They're NOT Looking For

- Production-ready system (it's a PoC)
- Complex infrastructure (local execution preferred)
- Comprehensive test coverage (thoughtful examples sufficient)
- Over-engineering (6-8 hour constraint)

### Interview Context

This is a **take-home exercise** followed by a **presentation interview**. The code is a conversation starter, not the end product. Architectural thinking, trade-off analysis, and communication are as important as working code.
