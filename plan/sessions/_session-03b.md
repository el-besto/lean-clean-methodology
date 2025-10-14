## Claude Instructions - Session 3b: Pragmatic CA Implementation

### Context
We have Steel Thread implementation (Session 03a) working. Now we evolve it to Pragmatic CA for production readiness.

### Your Task for This Session

**Evolve Steel Thread → Pragmatic CA** (Component D - Lean-Clean Code Paradigms, Part 2 of 3)

Specifically:
1. Take the working Steel Thread implementation from Session 03a
2. Add orchestrators, presenters, and infrastructure split
3. Organize drivers for enterprise integration (REST when needed)
4. Keep acceptance tests passing (same stakeholder contract)
5. Document the evolution patterns

### Your Role in This Session

You are my implementation architect for the **Pragmatic CA PoC type**. I need you to:

1. **Evolve, don't rewrite**: Add layers to Steel Thread, don't replace code

2. **Demonstrate Pragmatic CA patterns clearly**:
   - Orchestrators (coordinate multiple use cases)
   - Presenters (format responses, telemetry)
   - Infrastructure split (`adapters/` + `infrastructure/`)
   - Organized drivers (`drivers/{cli,rest,ui}/`)
   - Still co-located ports (don't extract yet)

3. **Keep it realistic**: Continue the campaign generation scenario from 03a

4. **Maintain tests**: Acceptance tests from 03a should still pass

5. **Show enterprise readiness**: Add REST API, better DI, production patterns

6. **Document the migration**: Show exactly what changed from Steel Thread

### Target Folder Structure (Pragmatic CA)

**Project Location:** `examples/02-pragmatic-ca-campaign-generator/`

```
examples/02-pragmatic-ca-campaign-generator/
├─ app/
│  ├─ entities/                        # Enhanced domain models
│  │  ├─ campaign.py
│  │  ├─ brand_guidelines.py           # Extracted value object
│  │  └─ errors.py                     # Domain errors
│  │
│  ├─ use_cases/                       # Business logic (no DTOs yet)
│  │  ├─ generate_campaign_uc.py
│  │  ├─ validate_brand_uc.py
│  │  └─ emit_events_uc.py
│  │
│  ├─ interface_adapters/              # NEW: Orchestrators + Presenters
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py   # Coordinates multiple use cases
│  │  └─ presenters/
│  │     └─ campaign_presenter.py      # Format responses, telemetry
│  │
│  ├─ adapters/                        # External services
│  │  ├─ imagegen/
│  │  │  ├─ protocol.py                # Co-located (not extracted yet)
│  │  │  ├─ fake.py
│  │  │  ├─ openai.py
│  │  │  └─ firefly.py                 # NEW: Additional implementation
│  │  ├─ storage/
│  │  │  ├─ protocol.py
│  │  │  ├─ fake.py
│  │  │  └─ s3.py                      # NEW: Real cloud storage
│  │  └─ events/
│  │     ├─ protocol.py
│  │     ├─ fake.py
│  │     └─ amplitude.py               # NEW: Real analytics
│  │
│  └─ infrastructure/                  # NEW: Persistence layer
│     └─ repositories/
│        └─ campaign/
│           ├─ protocol.py             # Co-located
│           ├─ in_memory.py            # In-memory fake
│           └─ postgres.py             # Real database
│
├─ drivers/                            # ORGANIZED: Split by entry point
│  ├─ cli/
│  │  ├─ __main__.py
│  │  ├─ commands.py
│  │  └─ dependencies.py               # DI module
│  │
│  ├─ rest/                            # NEW: Enterprise integration
│  │  ├─ main.py                       # FastAPI app
│  │  ├─ routers/
│  │  │  └─ campaigns.py
│  │  ├─ schemas/                      # API DTOs (Pydantic)
│  │  │  ├─ campaign_request.py
│  │  │  └─ campaign_response.py
│  │  └─ dependencies.py
│  │
│  └─ ui/
│     └─ streamlit/
│        ├─ app.py
│        ├─ pages/
│        │  ├─ 1_generate.py          # Creative Lead workflow
│        │  └─ 2_approve.py           # Legal/Compliance workflow
│        └─ dependencies.py
│
├─ tests/                              # ORGANIZED: Better structure
│  ├─ acceptance/
│  │  └─ features/
│  │     └─ test_localized_campaign_feature.py
│  ├─ unit/
│  │  ├─ use_cases/
│  │  ├─ orchestrators/                # NEW: Test orchestrators
│  │  └─ entities/
│  └─ integration/
│     ├─ adapters/
│     │  ├─ test_openai_imagegen.py
│     │  └─ test_s3_storage.py
│     └─ repositories/
│        └─ test_postgres_campaign_repo.py
│
├─ tools/                              # NEW: Development utilities
│  ├─ validate.py
│  └─ init_db.py
│
├─ .streamlit/
│  └─ config.toml
│
├─ pyproject.toml
├─ Makefile                            # NEW: Developer commands
├─ docker-compose.yaml                 # NEW: Local stack
└─ README.md
```

### Success Criteria
- [ ] Evolved from Steel Thread (not rewritten)
- [ ] Acceptance tests from 03a still pass (same contract)
- [ ] Orchestrators coordinate multiple use cases
- [ ] Presenters format responses and handle telemetry
- [ ] Infrastructure split (`adapters/` + `infrastructure/`)
- [ ] REST API works (FastAPI)
- [ ] Enhanced Streamlit UI with multiple pages
- [ ] Migration guide documented (what changed from 03a)
- [ ] All code follows Lean-Clean Python Style Guide

### Migration Strategy from Steel Thread

Follow this step-by-step process (documented in [framework-folder-structures.md §5.2](../../docs/framework-folder-structures.md#52-migration-checklist-steel-thread--pragmatic-ca)):

1. **First, copy Steel Thread project** (5 minutes)
   - `cp -r examples/01-steel-thread-campaign-generator examples/02-pragmatic-ca-campaign-generator`
   - Verify tests still pass before any changes
   - Create `MIGRATION.md` to track changes

2. **Add Infrastructure Layer**
   ```bash
   mkdir -p app/infrastructure/repositories/campaign
   # Move any repository code from adapters if needed
   ```

3. **Add Orchestrators**
   ```bash
   mkdir -p app/interface_adapters/orchestrators
   # Extract orchestration logic from drivers → orchestrator
   ```

4. **Add Presenters**
   ```bash
   mkdir -p app/interface_adapters/presenters
   # Add response formatting and telemetry methods
   ```

5. **Organize Drivers**
   ```bash
   mkdir -p drivers/{cli,rest/schemas,ui/streamlit/pages}
   mv drivers/cli.py drivers/cli/__main__.py
   mv drivers/ui/streamlit_app.py drivers/ui/streamlit/app.py
   # Add drivers/rest/ for enterprise integration
   ```

6. **Enhance Entities**
   - Add domain methods to entities
   - Add `errors.py` for domain errors
   - Extract `brand_guidelines.py` if needed

7. **Organize Tests**
   - Add test folders with subfolders
   - Add integration tests for repositories
   - Keep acceptance tests using fakes

8. **Add Real Adapters**
   - Implement `S3StorageAdapter`, `FireflyImageGenerator`
   - Write integration tests for real adapters
   - Keep fakes for acceptance tests

9. **Document Changes** (CRITICAL)
   - Update `MIGRATION.md` as you complete each step
   - For each step, list: files added, files modified, files deleted
   - Reference framework-folder-structures.md §5.2 steps explicitly
   - If you encounter a pattern not in the framework doc, add it to `DISCOVERED_PATTERNS.md`

### Anti-Patterns to Avoid

❌ **Don't rewrite from scratch**
   - Evolve the Steel Thread code incrementally
   - Acceptance tests should remain valid
   - Add layers, don't replace

❌ **Don't extract ports yet**
   - Keep `protocol.py` co-located with implementations
   - Port extraction happens in Full CA (Session 03c)
   - Co-location maintains simplicity for Pragmatic CA

❌ **Don't add use case DTOs yet**
   - Use cases use domain objects directly
   - DTOs only at `drivers/rest/schemas/` for FastAPI
   - Use case DTOs come in Full CA

❌ **Don't over-engineer the DI**
   - Manual DI in `dependencies.py` modules is fine
   - No need for complex DI containers yet
   - Keep it simple and explicit

❌ **Don't break acceptance tests**
   - Tests from 03a should still pass
   - If they don't, you've broken the stakeholder contract
   - Evolution should be additive, not destructive

❌ **Don't skip the migration documentation**
   - Update `MIGRATION.md` after completing each step
   - List all files added, modified, deleted for each step
   - Reference framework-folder-structures.md §5.2 explicitly
   - Capture any new patterns in `DISCOVERED_PATTERNS.md`

### Why This Matters

This session produces the **Pragmatic CA golden example** that:
- Shows how to evolve Steel Thread to production-ready architecture
- Demonstrates orchestrators, presenters, and infrastructure split
- Proves the evolution path works (tests still pass)
- Validates the standardized migration steps in framework doc
- Captures any gaps in framework doc via DISCOVERED_PATTERNS.md
- Provides the baseline for Full CA evolution (Session 03c)
- Shows enterprise integration patterns (REST API, organized drivers)

**The goal:** Demonstrate that you can take a working Steel Thread and evolve it to production-ready Pragmatic CA without breaking the stakeholder contract (acceptance tests), while documenting the exact changes made.

---

## Inputs You'll Provide

When you're ready to run this session, you'll provide:
1. **Steel Thread project** - From Session 03a (`examples/01-steel-thread-campaign-generator/`)
2. **Enterprise requirements** - REST API needs, additional stakeholders
3. **Any additional constraints** - Performance requirements, compliance needs

---

## Outputs This Session Delivers

At the end of this session, you'll have:
1. ✅ `examples/02-pragmatic-ca-campaign-generator/` - Production-ready project
2. ✅ All tests from 03a still passing (contract preserved)
3. ✅ Working orchestrators coordinating multiple use cases
4. ✅ Working presenters formatting responses
5. ✅ Working REST API (FastAPI)
6. ✅ Enhanced Streamlit UI with multiple pages
7. ✅ Infrastructure split (`adapters/` + `infrastructure/`)
8. ✅ MIGRATION.md documenting evolution from Steel Thread
9. ✅ DISCOVERED_PATTERNS.md (if any new patterns found)
10. ✅ Ready to evolve to Full CA (Session 03c)

### Project Documentation Structure

```
examples/02-pragmatic-ca-campaign-generator/
├─ README.md                       # Pragmatic CA patterns and usage
├─ MIGRATION.md                    # Evolution from Steel Thread
├─ DISCOVERED_PATTERNS.md          # New patterns not in framework doc (if any)
├─ app/
├─ drivers/
└─ tests/
```

**MIGRATION.md Format:**
```markdown
# Migration from Steel Thread to Pragmatic CA

Based on [framework-folder-structures.md §5.2](../../docs/framework-folder-structures.md#52-migration-checklist-steel-thread--pragmatic-ca)

## Migration Checklist

### 1. Added Infrastructure Layer ✅
- Created `app/infrastructure/repositories/campaign/`
- [List files added, modified, deleted]

### 2. Added Orchestrators ✅
- [Details...]

### 3-7. [Continue for all 7 steps]

## Summary of Changes
- **Total Files Added:** X
- **Total Files Modified:** Y
- **Total Files Deleted:** Z
- **Acceptance Tests Still Pass:** ✅ Yes

## Additional Patterns
See `DISCOVERED_PATTERNS.md` for patterns not covered by framework doc.
```

**DISCOVERED_PATTERNS.md Format:**
```markdown
# Discovered Patterns During Implementation

Patterns discovered that are NOT in [framework-folder-structures.md](../../docs/framework-folder-structures.md).

## Pattern 1: [Name]
**Context:** When evolving from Steel Thread to Pragmatic CA
**Problem:** [Issue not covered by migration checklist]
**Solution:** [How addressed]
**Bash Commands:** [Commands that worked]
**Recommendation:** Add as step X.Y in §5.2 of framework doc
```

---

**Session Status:** 📋 Defined - Ready to Execute

**Prerequisite:** Session 03a complete (Steel Thread working)

**Next Session:** 03c - Full CA Implementation (refine from Pragmatic CA)
