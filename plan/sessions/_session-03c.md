## Claude Instructions - Session 3c: Full CA Implementation

### Context
We have Pragmatic CA implementation (Session 03b) working. Now we refine it to Full CA for production scale.

### Your Task for This Session

**Refine Pragmatic CA → Full CA** (Component D - Lean-Clean Code Paradigms, Part 3 of 3)

Specifically:
1. Take the working Pragmatic CA implementation from Session 03b
2. Extract ports to `application/ports/` (formalize interfaces)
3. Add use case DTOs (protect domain boundaries)
4. Separate entities from ORM models (domain purity)
5. Add domain events and richer aggregates
6. Organize application layer (deep nesting for scale)
7. Keep acceptance tests passing (same stakeholder contract)
8. Document the refinement patterns

### Your Role in This Session

You are my implementation architect for the **Full CA PoC type**. I need you to:

1. **Refine, don't rewrite**: Formalize the Pragmatic CA architecture

2. **Demonstrate Full CA patterns clearly**:
   - Extracted ports (`application/ports/`)
   - Use case DTOs (protect domain from drivers)
   - Separate entities from ORM (mapping layer)
   - Domain events (aggregates emit events)
   - Organized application layer (deep nesting)
   - Production middleware (auth, logging, telemetry)

3. **Keep it realistic**: Continue the campaign generation scenario from 03b

4. **Maintain tests**: Acceptance tests from 03a/03b should still pass

5. **Show production scale**: Kubernetes, migrations, monitoring

6. **Document the refinement**: Show exactly what changed from Pragmatic CA

### Target Folder Structure (Full CA)

**Authoritative Reference:** See [`docs/framework-folder-structures.md` §4.1](../../docs/framework-folder-structures.md#41-folder-structure) for the complete Full CA structure with detailed explanations.

**Project Location:** `examples/03-full-ca-campaign-generator/`

```
examples/03-full-ca-campaign-generator/
├─ app/
│  ├─ entities/                        # REFINED: Rich aggregates with events
│  │  ├─ campaign/
│  │  │  ├─ campaign.py                # Campaign aggregate root
│  │  │  ├─ creative_set.py            # CreativeSet entity
│  │  │  ├─ creative.py                # Creative value object
│  │  │  └─ events.py                  # CampaignCreated, CreativeGenerated
│  │  ├─ brand/
│  │  │  ├─ brand.py                   # Brand aggregate root
│  │  │  └─ guidelines.py              # BrandGuidelines value object
│  │  └─ shared/
│  │     ├─ errors.py                  # Domain errors hierarchy
│  │     └─ value_objects.py           # Money, MarketCode, PlatformType
│  │
│  ├─ application/                     # NEW: Organized application layer
│  │  ├─ use_cases/                    # Use cases with DTOs
│  │  │  ├─ generate_campaign/
│  │  │  │  ├─ use_case.py            # GenerateCampaignUseCase
│  │  │  │  └─ dtos.py                # NEW: Use case DTOs
│  │  │  ├─ validate_brand/
│  │  │  │  ├─ use_case.py
│  │  │  │  └─ dtos.py
│  │  │  └─ emit_events/
│  │  │     ├─ use_case.py
│  │  │     └─ dtos.py
│  │  │
│  │  └─ ports/                        # NEW: Extracted from co-located
│  │     ├─ services/                  # External service contracts
│  │     │  ├─ image_generator.py     # IImageGenerator protocol
│  │     │  ├─ storage_adapter.py     # IStorageAdapter protocol
│  │     │  └─ event_tracker.py       # IEventTracker protocol
│  │     └─ repositories/              # Persistence contracts
│  │        └─ campaign_repository.py  # ICampaignRepository protocol
│  │
│  ├─ interface_adapters/              # Same as Pragmatic CA
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py   # Now converts: API DTO → UC DTO → domain
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  │
│  ├─ adapters/                        # External services (no protocol.py)
│  │  ├─ imagegen/
│  │  │  ├─ fake.py
│  │  │  ├─ openai.py
│  │  │  └─ firefly.py
│  │  ├─ storage/
│  │  │  ├─ fake.py
│  │  │  ├─ s3.py
│  │  │  └─ azure_blob.py              # NEW: Additional cloud provider
│  │  └─ events/
│  │     ├─ fake.py
│  │     └─ amplitude.py
│  │
│  └─ infrastructure/                  # Persistence (no protocol.py)
│     ├─ orm_models/                   # NEW: Separate from entities
│     │  ├─ campaign_orm.py
│     │  └─ creative_orm.py
│     ├─ repositories/
│     │  └─ campaign/
│     │     ├─ in_memory.py
│     │     ├─ postgres.py             # Now includes mapping layer
│     │     └─ mongodb.py              # NEW: Alternative persistence
│     └─ events/
│        ├─ fake_event_bus.py
│        └─ kafka_event_bus.py         # NEW: Real event bus
│
├─ drivers/                            # Production-ready drivers
│  ├─ cli/
│  │  ├─ __main__.py
│  │  ├─ commands/                     # NEW: Organized commands
│  │  │  ├─ generate.py
│  │  │  ├─ validate.py
│  │  │  └─ approve.py
│  │  ├─ formatters/                   # NEW: Output formatters
│  │  │  ├─ table.py
│  │  │  └─ json.py
│  │  └─ dependencies.py
│  │
│  ├─ rest/
│  │  ├─ main.py
│  │  ├─ routers/
│  │  │  ├─ campaigns.py
│  │  │  ├─ approvals.py
│  │  │  └─ webhooks.py                # NEW: Webhooks
│  │  ├─ schemas/                      # API DTOs (separate from UC DTOs)
│  │  │  ├─ campaign_request.py
│  │  │  ├─ campaign_response.py
│  │  │  └─ approval_request.py
│  │  ├─ middleware/                   # NEW: Production middleware
│  │  │  ├─ auth.py
│  │  │  ├─ logging.py
│  │  │  └─ telemetry.py               # OpenTelemetry
│  │  ├─ exception_handlers.py
│  │  └─ dependencies.py
│  │
│  └─ ui/
│     └─ streamlit/
│        ├─ app.py
│        ├─ pages/
│        │  ├─ 1_generate.py
│        │  ├─ 2_approve.py
│        │  ├─ 3_monitor.py            # NEW: IT monitoring
│        │  └─ 4_compliance.py         # NEW: Legal compliance
│        ├─ components/                # NEW: Reusable components
│        │  ├─ campaign_card.py
│        │  └─ approval_widget.py
│        └─ dependencies.py
│
├─ tests/
│  ├─ acceptance/                      # Same contract as 03a/03b
│  │  └─ features/
│  │     └─ test_localized_campaign_feature.py
│  ├─ unit/
│  │  ├─ entities/
│  │  ├─ use_cases/
│  │  ├─ orchestrators/
│  │  └─ presenters/
│  ├─ integration/
│  │  ├─ adapters/
│  │  └─ repositories/
│  └─ e2e/                             # NEW: Full system tests
│     └─ test_production_workflow.py
│
├─ alembic/                            # NEW: Database migrations
│  ├─ versions/
│  └─ env.py
│
├─ k8s/                                # NEW: Kubernetes manifests
│  ├─ deployment.yaml
│  ├─ service.yaml
│  └─ ingress.yaml
│
├─ .streamlit/
│  └─ config.toml
│
├─ tools/
│  ├─ validate.py
│  └─ init_db.py
│
├─ pyproject.toml
├─ Makefile
├─ docker-compose.yaml
├─ docker-compose.prod.yaml            # NEW: Production compose
└─ README.md
```

### Success Criteria
- [ ] Refined from Pragmatic CA (not rewritten)
- [ ] Acceptance tests from 03a/03b still pass (same contract)
- [ ] Ports extracted to `application/ports/`
- [ ] Use case DTOs protect domain boundaries
- [ ] Entities separated from ORM models
- [ ] Domain events implemented (aggregate roots)
- [ ] Production middleware (auth, logging, telemetry)
- [ ] Database migrations (Alembic)
- [ ] Kubernetes manifests for deployment
- [ ] E2E tests for full system
- [ ] Refinement guide documented (what changed from 03b)
- [ ] All code follows Lean-Clean Python Style Guide

### Migration Strategy from Pragmatic CA

Follow this step-by-step process (documented in [framework-folder-structures.md §5.3](../../docs/framework-folder-structures.md#53-migration-checklist-pragmatic-ca--full-ca)):

1. **First, copy Pragmatic CA project** (5 minutes)
   - `cp -r examples/02-pragmatic-ca-campaign-generator examples/03-full-ca-campaign-generator`
   - Verify tests still pass before any changes
   - Create `REFINEMENT.md` to track changes

2. **Extract Ports**
   ```bash
   # Create ports structure
   mkdir -p app/application/ports/{services,repositories}

   # Move service ports
   mv app/adapters/imagegen/protocol.py app/application/ports/services/image_generator.py
   mv app/adapters/storage/protocol.py app/application/ports/services/storage_adapter.py

   # Move repository ports
   mv app/infrastructure/repositories/campaign/protocol.py app/application/ports/repositories/campaign_repository.py

   # Update imports (find-replace)
   # From: from app.adapters.imagegen.protocol import IImageGenerator
   # To:   from app.application.ports.services.image_generator import IImageGenerator
   ```

3. **Add Use Case DTOs**
   ```bash
   # Organize use cases into folders
   mkdir -p app/application/use_cases/generate_campaign
   mv app/use_cases/generate_campaign_uc.py app/application/use_cases/generate_campaign/use_case.py

   # Create DTOs file
   touch app/application/use_cases/generate_campaign/dtos.py
   ```

4. **Organize Application Layer**
   ```bash
   mkdir -p app/application
   mv app/use_cases app/application/use_cases
   # Ports already in app/application/ports/ from step 2
   ```

5. **Separate Entities from ORM**
   - Ensure entities are pure (no SQLAlchemy annotations)
   - Create ORM models in `infrastructure/orm_models/`
   - Implement repository mapping layer
   - Add domain events to aggregates

6. **Add Production Middleware**
   ```bash
   mkdir -p drivers/rest/middleware
   # Implement auth, logging, telemetry middleware
   ```

7. **Add Production Infrastructure**
   - Add Kubernetes manifests (`k8s/`)
   - Add Alembic migrations (`alembic/`)
   - Add E2E tests (`tests/e2e/`)
   - Add monitoring setup

8. **Document Changes** (CRITICAL)
   - Update `REFINEMENT.md` as you complete each step
   - For each step, list: files added, files modified, files moved, files deleted
   - Reference framework-folder-structures.md §5.3 steps explicitly
   - If you encounter a pattern not in the framework doc, add it to `DISCOVERED_PATTERNS.md`

### Anti-Patterns to Avoid

❌ **Don't rewrite from scratch**
   - Refine the Pragmatic CA code incrementally
   - Acceptance tests should remain valid
   - Refine structure, don't replace functionality

❌ **Don't break imports unnecessarily**
   - When extracting ports, use find-replace for imports
   - Verify tests pass after each migration step
   - Don't create import chaos

❌ **Don't over-engineer the DTOs**
   - Use case DTOs should be simple data containers
   - Don't add business logic to DTOs
   - Keep domain logic in entities and use cases

❌ **Don't couple entities to ORM**
   - Entities should be pure Python (dataclasses)
   - No SQLAlchemy imports in entities
   - Repository handles all mapping

❌ **Don't skip the E2E tests**
   - Full CA needs full system tests
   - Test the deployed system, not just units
   - Smoke tests before production deploy

❌ **Don't ignore production concerns**
   - Add authentication, logging, telemetry
   - Add database migrations (Alembic)
   - Add deployment manifests (Kubernetes)

❌ **Don't skip the refinement documentation**
   - Update `REFINEMENT.md` after completing each step
   - List all files added, modified, moved, deleted for each step
   - Reference framework-folder-structures.md §5.3 explicitly
   - Capture any new patterns in `DISCOVERED_PATTERNS.md`

### Why This Matters

This session produces the **Full CA golden example** that:
- Shows how to refine Pragmatic CA to production-scale architecture
- Demonstrates extracted ports, use case DTOs, domain purity
- Proves the evolution path works (tests still pass)
- Validates the standardized refinement steps in framework doc
- Captures any gaps in framework doc via DISCOVERED_PATTERNS.md
- Provides the complete reference implementation
- Shows production-ready patterns (middleware, migrations, K8s)
- Completes the full evolution trilogy: Steel Thread → Pragmatic CA → Full CA

**The goal:** Demonstrate that you can take a working Pragmatic CA and refine it to production-scale Full CA without breaking the stakeholder contract (acceptance tests), while documenting the exact changes made and capturing any new patterns for the framework doc.

---

## Inputs You'll Provide

When you're ready to run this session, you'll provide:
1. **Pragmatic CA project** - From Session 03b (`examples/02-pragmatic-ca-campaign-generator/`)
2. **Production requirements** - Scale, multi-team needs, compliance
3. **Any additional constraints** - Performance SLAs, security requirements

---

## Outputs This Session Delivers

At the end of this session, you'll have:
1. ✅ `examples/03-full-ca-campaign-generator/` - Production-scale project
2. ✅ All tests from 03a/03b still passing (contract preserved)
3. ✅ Extracted ports in `application/ports/`
4. ✅ Use case DTOs protecting domain boundaries
5. ✅ Separate entities from ORM (mapping layer)
6. ✅ Domain events and rich aggregates
7. ✅ Production middleware (auth, logging, telemetry)
8. ✅ Database migrations (Alembic)
9. ✅ Kubernetes manifests for deployment
10. ✅ E2E tests for full system
11. ✅ REFINEMENT.md documenting evolution from Pragmatic CA
12. ✅ DISCOVERED_PATTERNS.md (if any new patterns found)
13. ✅ Complete golden example showing Steel Thread → Pragmatic CA → Full CA evolution

### Project Documentation Structure

```
examples/03-full-ca-campaign-generator/
├─ README.md                       # Full CA patterns and usage
├─ REFINEMENT.md                   # Evolution from Pragmatic CA
├─ DISCOVERED_PATTERNS.md          # New patterns not in framework doc (if any)
├─ app/
├─ drivers/
├─ tests/
├─ alembic/
└─ k8s/
```

**REFINEMENT.md Format:**
```markdown
# Refinement from Pragmatic CA to Full CA

Based on [framework-folder-structures.md §5.3](../../docs/framework-folder-structures.md#53-migration-checklist-pragmatic-ca--full-ca)

## Refinement Checklist

### 1. Extracted Ports ✅
- Moved `protocol.py` files to `application/ports/`
- [List import changes, files moved]

### 2. Added Use Case DTOs ✅
- [Details...]

### 3-6. [Continue for all 6 steps]

## Summary of Changes
- **Total Files Added:** X
- **Total Files Modified:** Y
- **Total Files Moved:** Z
- **Acceptance Tests Still Pass:** ✅ Yes

## Additional Patterns
See `DISCOVERED_PATTERNS.md` for patterns not covered by framework doc.
```

**DISCOVERED_PATTERNS.md Format:**
```markdown
# Discovered Patterns During Implementation

Patterns discovered that are NOT in [framework-folder-structures.md](../../docs/framework-folder-structures.md).

## Pattern 1: [Name]
**Context:** When refining from Pragmatic CA to Full CA
**Problem:** [Issue not covered by refinement checklist]
**Solution:** [How addressed]
**Bash Commands:** [Commands that worked]
**Recommendation:** Add as step X.Y in §5.3 of framework doc
```

---

**Session Status:** 📋 Defined - Ready to Execute

**Prerequisite:** Session 03b complete (Pragmatic CA working)

**Next Session:** 04a - Extract Scaffolds (create templates from golden examples)
