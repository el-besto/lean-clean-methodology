# Campaign Generator: Use Cases

**Status:** Ready for Implementation
**Based On:** Resolved architectural decisions + domain entities
**Architecture:** Pragmatic Clean Architecture (Decision 1)

---

## Overview

This document defines the three core use cases for the Campaign Generator PoC. Each use case represents a distinct business capability, following Clean Architecture principles where use cases contain business logic and depend on entity interfaces (not implementations).

**Use Case Location:** `app/use_cases/`

**Design Principles:**
- Use cases are independent of frameworks and UI
- Use cases depend on entity protocols/interfaces
- Use cases orchestrate entity interactions
- Use cases return result objects (not direct entities)

---

## Use Case 1: Understand Brand

**File:** `app/use_cases/understand_brand_uc.py`

### Purpose

Analyze brand assets (logos, images, colors) to extract brand identity and guidelines using Claude Vision API. Supports brand consistency across campaigns (Business Goal 2).

### Input

```python
@dataclass
class UnderstandBrandInput:
    """Input for brand understanding use case."""
    input_artifacts: List[str]  # Paths to brand assets (logos, images)
    brand_name: Optional[str]  # Optional brand name hint
    analysis_depth: str = "standard"  # "minimal" | "standard" | "comprehensive"
```

### Output

```python
@dataclass
class UnderstandBrandResult:
    """Result of brand understanding analysis."""
    success: bool
    brand_summary: Optional[BrandSummary]
    similar_brands: List[BrandSummary]  # From Weaviate search (optional)
    confidence_score: float  # 0.0 to 1.0
    analysis_notes: str
    error: Optional[str]
```

### Business Logic

```python
class UnderstandBrandUseCase:
    """
    Use case for understanding brand identity from visual assets.

    Dependencies (injected):
    - IAIAdapter: For Claude Vision API calls
    - IBrandRepository: For searching similar brands (Weaviate)
    - IStorageAdapter: For loading brand assets
    """

    def __init__(
        self,
        ai_adapter: IAIAdapter,
        brand_repository: IBrandRepository,
        storage_adapter: IStorageAdapter
    ):
        self.ai_adapter = ai_adapter
        self.brand_repository = brand_repository
        self.storage_adapter = storage_adapter

    def execute(self, input_data: UnderstandBrandInput) -> UnderstandBrandResult:
        """
        Execute brand understanding workflow.

        Steps:
        1. Load brand assets from storage
        2. Analyze assets using Claude Vision API
        3. Extract brand attributes (colors, voice, style)
        4. Search for similar brands in repository (optional)
        5. Return brand summary with confidence score
        """
        try:
            # Step 1: Load assets
            assets = [self.storage_adapter.load(path) for path in input_data.input_artifacts]

            # Step 2: Analyze with Claude Vision
            analysis_prompt = self._build_analysis_prompt(input_data.brand_name, input_data.analysis_depth)
            brand_attributes = self.ai_adapter.analyze_images(assets, analysis_prompt)

            # Step 3: Create BrandSummary entity
            brand_summary = self._create_brand_summary(brand_attributes, input_data.brand_name)

            # Step 4: Search similar brands (Weaviate)
            similar_brands = []
            if brand_summary.validate():
                similar_brands = self.brand_repository.find_similar(brand_summary, limit=3)

            # Step 5: Return result
            return UnderstandBrandResult(
                success=True,
                brand_summary=brand_summary,
                similar_brands=similar_brands,
                confidence_score=self._calculate_confidence(brand_attributes),
                analysis_notes=f"Analyzed {len(assets)} assets",
                error=None
            )

        except Exception as e:
            return UnderstandBrandResult(
                success=False,
                brand_summary=None,
                similar_brands=[],
                confidence_score=0.0,
                analysis_notes="",
                error=str(e)
            )

    def _build_analysis_prompt(self, brand_name: Optional[str], depth: str) -> str:
        """Build Claude Vision analysis prompt."""
        # Implementation details...

    def _create_brand_summary(self, attributes: dict, brand_name: Optional[str]) -> BrandSummary:
        """Create BrandSummary entity from AI analysis."""
        # Implementation details...

    def _calculate_confidence(self, attributes: dict) -> float:
        """Calculate confidence score based on attribute completeness."""
        # Implementation details...
```

### Dependencies

**Required Adapters:**
- `IAIAdapter` (Claude Vision): For image analysis
- `IBrandRepository` (Weaviate/YAML): For brand search
- `IStorageAdapter` (Local/MinIO): For loading assets

**Entities Used:**
- `BrandSummary`: Output entity

### Validation Rules

1. At least one input artifact required
2. Supported image formats: PNG, JPG, WEBP
3. Maximum file size: 20MB per asset
4. Brand summary must pass `validate()` method
5. Confidence score < 0.5 triggers warning alert

### Error Handling

- **FileNotFoundError**: Asset path invalid → Return error with specific path
- **AIAdapterError**: API failure → Retry once, then return error
- **ValidationError**: Invalid brand summary → Return error with validation details

### HITL Checkpoints (Decision 9: Simulated)

**Checkpoint:** `accept_brand_choice`
- **Trigger:** After similar brands found
- **Action:** Log approval checkpoint with brand options
- **Implementation:** Create `Approval` entity with status PENDING (auto-approved in PoC)

### Task 3 Extensions (Agentic System)

**Monitoring:**
- Alert if confidence score < 0.5: `ALERT_TYPE.INSUFFICIENT_CONFIDENCE`
- Alert if no similar brands found: `ALERT_TYPE.BRAND_MISMATCH_RISK`
- Alert if Claude Vision API fails: `ALERT_TYPE.API_ERROR`

---

## Use Case 2: Generate Campaign

**File:** `app/use_cases/generate_campaign_uc.py`

### Purpose

Generate complete campaign creative assets (images + localized messages) for all products, aspect ratios, and locales specified in campaign brief. Core automation capability (Business Goal 1).

### Input

```python
@dataclass
class GenerateCampaignInput:
    """Input for campaign generation use case."""
    campaign_brief: CampaignBrief
    brand_summary: BrandSummary
    reuse_existing: bool = True  # Decision 8: Asset reuse strategy
```

### Output

```python
@dataclass
class GenerateCampaignResult:
    """Result of campaign generation."""
    success: bool
    assets: List[CreativeAsset]
    reused_count: int
    generated_count: int
    failed_count: int
    generation_time_seconds: float
    validation_results: List[ValidationResult]
    error: Optional[str]
```

### Business Logic

```python
class GenerateCampaignUseCase:
    """
    Use case for generating campaign creative assets.

    Dependencies (injected):
    - IAIAdapter: For image generation (OpenAI DALL-E 3)
    - IAIAdapter: For text overlay (OpenAI API)
    - IAIAdapter: For localization (Claude Multilingual)
    - IStorageAdapter: For saving assets
    - IAssetRepository: For searching existing assets (reuse)
    """

    def __init__(
        self,
        image_ai_adapter: IAIAdapter,  # OpenAI for images
        text_ai_adapter: IAIAdapter,   # OpenAI for text overlay
        locale_ai_adapter: IAIAdapter,  # Claude for localization
        storage_adapter: IStorageAdapter,
        asset_repository: IAssetRepository
    ):
        self.image_ai = image_ai_adapter
        self.text_ai = text_ai_adapter
        self.locale_ai = locale_ai_adapter
        self.storage = storage_adapter
        self.asset_repo = asset_repository

    def execute(self, input_data: GenerateCampaignInput) -> GenerateCampaignResult:
        """
        Execute campaign generation workflow.

        Steps:
        1. Validate campaign brief
        2. Localize campaign slogans for all target locales (Decision 7)
        3. For each product × aspect ratio × locale:
           a. Check if asset exists (Decision 8: reuse strategy)
           b. If not exists: Generate hero image (OpenAI DALL-E 3)
           c. Add localized slogan to image (OpenAI text overlay)
           d. Create CreativeAsset entity
           e. Save to storage
        4. Return results with generation metrics
        """
        start_time = time.time()
        assets = []
        reused = 0
        generated = 0
        failed = 0

        try:
            # Step 1: Validate brief
            brief = input_data.campaign_brief
            if not brief.validate():
                raise ValueError("Invalid campaign brief")

            # Step 2: Localize slogans (Decision 7: English + Spanish)
            localized_slogans = self._localize_slogans(
                brief.campaign_slogan,
                brief.target_locales,
                input_data.brand_summary
            )

            # Step 3: Generate assets (product × aspect × locale)
            for product in brief.products:
                for aspect in brief.aspects:
                    for locale in brief.target_locales:
                        try:
                            asset = self._generate_or_reuse_asset(
                                brief=brief,
                                brand=input_data.brand_summary,
                                product=product,
                                aspect=aspect,
                                locale=locale,
                                slogan=localized_slogans[locale],
                                reuse=input_data.reuse_existing
                            )

                            if asset.reused:
                                reused += 1
                            else:
                                generated += 1

                            assets.append(asset)

                        except Exception as e:
                            failed += 1
                            logging.error(f"Failed to generate asset: {product.name}/{aspect}/{locale}: {e}")

            # Step 4: Return results
            generation_time = time.time() - start_time

            return GenerateCampaignResult(
                success=True,
                assets=assets,
                reused_count=reused,
                generated_count=generated,
                failed_count=failed,
                generation_time_seconds=generation_time,
                validation_results=[],  # Populated by ValidateCampaign use case
                error=None
            )

        except Exception as e:
            return GenerateCampaignResult(
                success=False,
                assets=[],
                reused_count=0,
                generated_count=0,
                failed_count=0,
                generation_time_seconds=time.time() - start_time,
                validation_results=[],
                error=str(e)
            )

    def _localize_slogans(
        self,
        slogan: str,
        locales: List[str],
        brand: BrandSummary
    ) -> Dict[str, str]:
        """
        Localize campaign slogan using Claude Multilingual API.

        Decision 7: English + Spanish (US) for PoC.
        """
        localized = {}
        for locale in locales:
            if locale == "en-US":
                localized[locale] = slogan
            else:
                # Use Claude to localize
                prompt = f"Translate this campaign slogan to {locale}, maintaining brand voice '{brand.voice_tone}': {slogan}"
                localized[locale] = self.locale_ai.generate_text(prompt)
        return localized

    def _generate_or_reuse_asset(
        self,
        brief: CampaignBrief,
        brand: BrandSummary,
        product: Product,
        aspect: str,
        locale: str,
        slogan: str,
        reuse: bool
    ) -> CreativeAsset:
        """
        Generate or reuse creative asset.

        Decision 8: Vector similarity search for reuse (deferred implementation).
        PoC: Filename convention matching.
        """
        # Check for existing asset (reuse)
        if reuse:
            existing = self.asset_repo.find_existing(
                product_name=product.name,
                aspect_ratio=aspect,
                locale=locale
            )
            if existing:
                return existing

        # Generate new asset
        # 1. Generate hero image (OpenAI DALL-E 3)
        image_prompt = self._build_image_prompt(brand, product, aspect)
        hero_image = self.image_ai.generate_image(image_prompt, aspect_ratio=aspect)

        # 2. Add text overlay (OpenAI API)
        final_image = self.text_ai.add_text_to_image(hero_image, slogan, brand.colors)

        # 3. Save to storage
        asset_id = f"{brief.brief_id}-{product.name}-{aspect}-{locale}-{uuid.uuid4().hex[:8]}"
        image_path = f"out/assets/{product.name}/{locale}/{aspect}/{asset_id}.png"
        self.storage.save(image_path, final_image)

        # 4. Create entity
        asset = CreativeAsset(
            asset_id=asset_id,
            brief_id=brief.brief_id,
            brand_id=brand.brand_id,
            product_name=product.name,
            audience=brief.target_audience,
            locale=locale,
            aspect_ratio=aspect,
            message=slogan,
            image_url=image_path,
            reused=False,
            generated_at=datetime.now(),
            meta={"image_prompt": image_prompt}
        )

        return asset

    def _build_image_prompt(self, brand: BrandSummary, product: Product, aspect: str) -> str:
        """Build DALL-E 3 image generation prompt."""
        # Implementation details...
```

### Dependencies

**Required Adapters:**
- `IAIAdapter` (OpenAI Image): For DALL-E 3 image generation
- `IAIAdapter` (OpenAI Text): For text overlay
- `IAIAdapter` (Claude Multilingual): For localization
- `IStorageAdapter` (Local/MinIO): For saving images
- `IAssetRepository` (Weaviate/Filesystem): For asset reuse

**Entities Used:**
- `CampaignBrief`: Input entity
- `BrandSummary`: Input entity
- `Product`: Embedded in CampaignBrief
- `CreativeAsset`: Output entity

### Validation Rules

1. Campaign brief must validate (minimum 2 products, 3 aspects)
2. All locales must be supported (en-US, es-US for PoC)
3. Generated images must meet minimum quality threshold
4. Slogans must fit within text overlay bounds

### Error Handling

- **OpenAI API Error**: Retry with exponential backoff (max 3 attempts)
- **Claude API Error**: Retry once, fall back to English if localization fails
- **Storage Error**: Log error, continue with next asset
- **Invalid Aspect Ratio**: Skip asset, log warning

### HITL Checkpoints (Decision 9: Simulated)

**Checkpoint:** `accept_campaign_images`
- **Trigger:** After all assets generated
- **Action:** Log approval checkpoint with asset count and sample images
- **Implementation:** Create `Approval` entity with status PENDING (auto-approved in PoC)

### Task 3 Extensions (Agentic System)

**Monitoring:**
- Alert if `failed_count > 0`: `ALERT_TYPE.GENERATION_FAILED`
- Alert if `generated_count < expected`: `ALERT_TYPE.INSUFFICIENT_VARIANTS`
- Alert if generation time > threshold: `ALERT_TYPE.PERFORMANCE_DEGRADATION`

---

## Use Case 3: Validate Campaign

**File:** `app/use_cases/validate_campaign_uc.py`

### Purpose

Validate generated campaign assets for brand compliance and legal content checks (Decision 10: Nice-to-have features). Supports Business Goal 2 (brand consistency).

### Input

```python
@dataclass
class ValidateCampaignInput:
    """Input for campaign validation use case."""
    assets: List[CreativeAsset]
    brand_summary: BrandSummary
    validation_checks: List[str]  # ["prohibited_words", "brand_colors", "logo_presence"]
    strict_mode: bool = False  # If True, warnings fail validation
```

### Output

```python
@dataclass
class ValidateCampaignResult:
    """Result of campaign validation."""
    success: bool
    validation_results: List[ValidationResult]
    passed_count: int
    failed_count: int
    warning_count: int
    summary: str
    error: Optional[str]
```

### Business Logic

```python
class ValidateCampaignUseCase:
    """
    Use case for validating campaign assets.

    Decision 10: Implement logging + legal checks (stub brand compliance).

    Dependencies (injected):
    - IValidationAdapter: For legal checks (prohibited words)
    - IBrandValidator: For brand compliance (logo, colors) - stubbed in PoC
    """

    def __init__(
        self,
        validation_adapter: IValidationAdapter,
        brand_validator: IBrandValidator
    ):
        self.validation_adapter = validation_adapter
        self.brand_validator = brand_validator

    def execute(self, input_data: ValidateCampaignInput) -> ValidateCampaignResult:
        """
        Execute validation workflow.

        Steps:
        1. For each creative asset:
           a. Run prohibited words check (Decision 10: implemented)
           b. Run brand color check (Decision 10: stubbed)
           c. Run logo presence check (Decision 10: stubbed)
        2. Aggregate results
        3. Create alerts for failures (Task 3)
        4. Return validation summary
        """
        validation_results = []
        passed = 0
        failed = 0
        warnings = 0

        try:
            for asset in input_data.assets:
                result = self._validate_asset(
                    asset,
                    input_data.brand_summary,
                    input_data.validation_checks,
                    input_data.strict_mode
                )

                validation_results.append(result)

                if result.status == ValidationStatus.PASSED:
                    passed += 1
                elif result.status == ValidationStatus.FAILED:
                    failed += 1
                elif result.status == ValidationStatus.WARNING:
                    warnings += 1

            summary = f"Validation: {passed}/{len(input_data.assets)} passed, {failed} failed, {warnings} warnings"

            return ValidateCampaignResult(
                success=(failed == 0),
                validation_results=validation_results,
                passed_count=passed,
                failed_count=failed,
                warning_count=warnings,
                summary=summary,
                error=None
            )

        except Exception as e:
            return ValidateCampaignResult(
                success=False,
                validation_results=[],
                passed_count=0,
                failed_count=0,
                warning_count=0,
                summary="Validation error",
                error=str(e)
            )

    def _validate_asset(
        self,
        asset: CreativeAsset,
        brand: BrandSummary,
        checks: List[str],
        strict: bool
    ) -> ValidationResult:
        """
        Validate single creative asset.
        """
        issues = []
        passed = 0
        failed = 0

        # Check 1: Prohibited words (Decision 10: implemented)
        if "prohibited_words" in checks:
            prohibited = self.validation_adapter.check_prohibited_words(asset.message)
            if prohibited:
                issues.append(ValidationIssue(
                    check_name="prohibited_words",
                    severity="error",
                    message=f"Prohibited words found: {prohibited}",
                    fix_suggestion="Remove prohibited words from message"
                ))
                failed += 1
            else:
                passed += 1

        # Check 2: Brand colors (Decision 10: stubbed)
        if "brand_colors" in checks:
            # Stub implementation - always passes with warning
            issues.append(ValidationIssue(
                check_name="brand_colors",
                severity="warning",
                message="Brand color validation not implemented (stubbed)",
                fix_suggestion="Implement image color analysis"
            ))
            passed += 1  # Stub passes

        # Check 3: Logo presence (Decision 10: stubbed)
        if "logo_presence" in checks:
            # Stub implementation - always passes with warning
            issues.append(ValidationIssue(
                check_name="logo_presence",
                severity="warning",
                message="Logo presence validation not implemented (stubbed)",
                fix_suggestion="Implement image object detection"
            ))
            passed += 1  # Stub passes

        # Determine status
        if failed > 0:
            status = ValidationStatus.FAILED
        elif strict and len(issues) > 0:
            status = ValidationStatus.FAILED
        elif len(issues) > 0:
            status = ValidationStatus.WARNING
        else:
            status = ValidationStatus.PASSED

        return ValidationResult(
            asset_id=asset.asset_id,
            status=status,
            checks_run=checks,
            issues=issues,
            passed_count=passed,
            failed_count=failed
        )
```

### Dependencies

**Required Adapters:**
- `IValidationAdapter`: For legal checks (prohibited words list)
- `IBrandValidator`: For brand compliance (stubbed in PoC)

**Entities Used:**
- `CreativeAsset`: Input entity
- `BrandSummary`: Input entity
- `ValidationResult`: Output entity
- `ValidationIssue`: Embedded in ValidationResult

### Validation Rules

1. **Prohibited Words** (Implemented):
   - Maintain list of prohibited words
   - Case-insensitive matching
   - Exact word boundaries

2. **Brand Colors** (Stubbed):
   - Extract dominant colors from image
   - Match against brand color palette
   - Threshold: 70% color similarity

3. **Logo Presence** (Stubbed):
   - Detect logo in generated image
   - Verify minimum size (10% of image)
   - Verify visibility (not obscured by text)

### Error Handling

- **ValidationAdapterError**: Log error, mark asset as SKIPPED
- **BrandValidatorError**: Log warning, continue with other checks

### HITL Checkpoints (Decision 9: Simulated)

**Checkpoint:** `validation_review`
- **Trigger:** If warnings or failures detected
- **Action:** Log validation results for stakeholder review
- **Implementation:** Create `Approval` entity with validation summary

### Task 3 Extensions (Agentic System)

**Monitoring:**
- Alert if `failed_count > 0`: `ALERT_TYPE.VALIDATION_FAILED`
- Alert if warnings > threshold: `ALERT_TYPE.COMPLIANCE_RISK`
- Email stakeholders with validation summary and flagged assets

---

## Use Case Composition

**Primary Workflow (Orchestrated by CampaignOrchestrator):**

```python
# app/interface_adapters/orchestrators/campaign_orchestrator.py

class CampaignOrchestrator:
    """
    Orchestrates campaign generation workflow using all three use cases.
    """

    def __init__(
        self,
        understand_brand_uc: UnderstandBrandUseCase,
        generate_campaign_uc: GenerateCampaignUseCase,
        validate_campaign_uc: ValidateCampaignUseCase
    ):
        self.understand_brand = understand_brand_uc
        self.generate_campaign = generate_campaign_uc
        self.validate_campaign = validate_campaign_uc

    def execute_full_workflow(self, campaign_brief: CampaignBrief) -> dict:
        """
        Full campaign generation workflow:
        1. Load/understand brand
        2. Generate campaign assets
        3. Validate assets
        4. Log approvals (Task 3 foundation)
        5. Return results
        """
        # Step 1: Understand brand (if needed)
        brand_result = self.understand_brand.execute(...)

        # Step 2: Generate campaign
        campaign_result = self.generate_campaign.execute(...)

        # Step 3: Validate campaign
        validation_result = self.validate_campaign.execute(...)

        # Step 4: Log approvals (Decision 9: simulated)
        self._log_approval_checkpoint("campaign_complete", ...)

        # Step 5: Return results
        return {...}
```

---

## Testing Strategy (Decision 12)

**One Feature-Level Test:** `tests/features/test_localized_campaign.py`

```python
def test_localized_campaign_generation():
    """
    Feature test: Generate localized campaign with 2 products, 3 aspects, 2 locales.

    Based on blog post pattern (Session 03a reference).
    """
    # Given: Campaign brief with 2 products, 3 aspects, 2 locales
    brief = load_campaign_brief("fixtures/holiday-gift-2025-01.yaml")

    # When: Execute full workflow
    orchestrator = build_campaign_orchestrator(use_fakes=True)
    result = orchestrator.execute_full_workflow(brief)

    # Then: Assert 12 assets generated (2×3×2)
    assert result["success"] is True
    assert len(result["assets"]) == 12
    assert result["validation"]["passed_count"] == 12

    # And: Assets organized by product/locale/aspect
    assert os.path.exists("out/assets/lavender-soap/en-US/1:1/")
    assert os.path.exists("out/assets/lavender-soap/es-US/9:16/")
```

---

## Next Steps

1. Implement use cases in `app/use_cases/`
2. Define adapter protocols in `app/adapters/*/protocol.py`
3. Implement fake adapters for testing
4. Create orchestrator in `app/interface_adapters/orchestrators/`
5. Wire dependencies in CLI driver

**Related Documents:**
- `function-composition.md`: Pipeline flow and function dependencies
- `architecture-diagram.md`: Layer interactions and data flow
- `entities.md`: Domain model definitions
