# Campaign Generator: Function Composition & Pipeline Flow

**Status:** Ready for Implementation
**Based On:** Use cases + domain entities + architectural decisions
**Architecture:** Pragmatic Clean Architecture

---

## Overview

This document defines the complete pipeline flow for campaign generation, showing how functions compose to deliver end-to-end automation. The pipeline orchestrates the three core use cases through a series of discrete, testable functions.

**Design Principles:**
- **Functional composition:** Small, focused functions that compose into larger workflows
- **Dependency injection:** All external dependencies (AI, storage) injected via adapters
- **Error propagation:** Clear error handling at each pipeline stage
- **Observability:** Logging and approval checkpoints throughout

---

## Pipeline Architecture

### High-Level Flow

```
INPUT (YAML Brief) → BRAND → GENERATION → VALIDATION → APPROVAL → OUTPUT (Organized Assets)
```

### Detailed Pipeline

```
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 1: BRAND UNDERSTANDING                                            │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  load_campaign_brief(yaml_path)                                         │
│         ↓                                                               │
│  load_or_understand_brand(brand_id, assets?)                            │
│         ├─→ [IF brand_id exists]: load_brand_from_repository()          │
│         └─→ [IF new brand]: understand_brand() → search_similar()       │
│                                    ↓                                    │
│              accept_brand_choice() ← HITL CHECKPOINT (simulated)        │
│                                                                         │
└───────────────────────────────┬─────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 2: LOCALIZATION                                                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  localize_campaign_slogans(slogan, target_locales, brand)               │
│         ↓                                                               │
│  {en-US: "Give the Gift of Nature", es-US: "Regala la Naturaleza"}      │
│                                                                         │
└───────────────────────────────┬─────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 3: ASSET GENERATION (Loop: product × aspect × locale)             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  FOR EACH (product, aspect_ratio, locale):                              │
│         ↓                                                               │
│  search_existing_asset(product, aspect, locale)                         │
│         ├─→ [IF found]: reuse_asset() → mark_as_reused()                │
│         └─→ [IF not found]:                                             │
│                   ↓                                                     │
│            generate_hero_image(product, brand, aspect)                  │
│                   ↓                                                     │
│            add_slogan_to_image(image, localized_slogan, brand_colors)   │
│                   ↓                                                     │
│            save_creative_asset(asset, storage_adapter)                  │
│                   ↓                                                     │
│            create_creative_asset_entity()                               │
│                                                                         │
└───────────────────────────────┬─────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 4: VALIDATION                                                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  FOR EACH creative_asset:                                               │
│         ↓                                                               │
│  validate_brand_compliance(asset, brand) → [STUBBED]                    │
│         ↓                                                               │
│  check_legal_content(asset.message) → [IMPLEMENTED]                     │
│         ↓                                                               │
│  create_validation_result(checks, issues)                               │
│                                                                         │
│  aggregate_validation_results()                                         │
│         ↓                                                               │
│  [IF failures]: create_alert(VALIDATION_FAILED)                         │
│                                                                         │
└───────────────────────────────┬─────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────────────┐
│ PHASE 5: APPROVAL & OUTPUT                                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  accept_campaign_images() ← HITL CHECKPOINT (simulated)                 │
│         ↓                                                               │
│  log_approval_checkpoint("campaign_complete", assets)                   │
│         ↓                                                               │
│  organize_output_folder(assets) → out/assets/{product}/{locale}/{aspect}│
│         ↓                                                               │
│  generate_campaign_report(metrics, validation_results)                  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Core Pipeline Functions

### Phase 1: Brand Understanding

#### 1.1 `load_campaign_brief(yaml_path: str) -> CampaignBrief`

**Purpose:** Load and parse campaign brief from YAML file (Decision 4)

**Implementation:**
```python
def load_campaign_brief(yaml_path: str) -> CampaignBrief:
    """
    Load campaign brief from YAML file.

    Raises:
        FileNotFoundError: If YAML file doesn't exist
        ValidationError: If YAML schema invalid
    """
    with open(yaml_path, 'r') as f:
        data = yaml.safe_load(f)

    brief = CampaignBrief(
        brief_id=data['brief_id'],
        brand_id=data['brand_id'],
        campaign_slogan=data['campaign_slogan'],
        target_region=data['target_region'],
        target_audience=data['target_audience'],
        target_locales=data['target_locales'],
        products=[Product(**p) for p in data['products']],
        aspects=data['aspects'],
        created_at=datetime.now()
    )

    if not brief.validate():
        raise ValidationError("Campaign brief validation failed")

    return brief
```

**Location:** `app/use_cases/generate_campaign_uc.py` (helper method)

**Dependencies:** None (pure function)

---

#### 1.2 `load_or_understand_brand(brand_id: str, assets: Optional[List[str]]) -> BrandSummary`

**Purpose:** Load existing brand or analyze new brand from assets

**Implementation:**
```python
def load_or_understand_brand(
    brand_id: str,
    assets: Optional[List[str]],
    brand_repo: IBrandRepository,
    understand_brand_uc: UnderstandBrandUseCase
) -> BrandSummary:
    """
    Load brand from repository or understand from assets.

    Decision 5: Brand as YAML config with adapter for Weaviate.
    """
    # Try loading existing brand
    brand = brand_repo.get_by_id(brand_id)
    if brand:
        return brand

    # If not found and assets provided, understand brand
    if assets:
        result = understand_brand_uc.execute(
            UnderstandBrandInput(
                input_artifacts=assets,
                brand_name=brand_id,
                analysis_depth="standard"
            )
        )
        if result.success:
            return result.brand_summary

    raise ValueError(f"Brand {brand_id} not found and no assets provided")
```

**Location:** `app/interface_adapters/orchestrators/campaign_orchestrator.py`

**Dependencies:**
- `IBrandRepository`: Load brand from storage
- `UnderstandBrandUseCase`: Analyze brand from assets

---

#### 1.3 `understand_brand(assets: List[str]) -> BrandSummary`

**Purpose:** Analyze brand assets using Claude Vision API

**Implementation:** See `use-cases.md` → UnderstandBrandUseCase

**Location:** `app/use_cases/understand_brand_uc.py`

**Dependencies:**
- `IAIAdapter` (Claude Vision): Image analysis
- `IStorageAdapter`: Load asset files
- `IBrandRepository`: Search similar brands

---

#### 1.4 `search_similar_brands(brand: BrandSummary) -> List[BrandSummary]`

**Purpose:** Find similar brands using vector similarity search (Weaviate)

**Implementation:**
```python
def search_similar_brands(
    brand: BrandSummary,
    brand_repo: IBrandRepository,
    limit: int = 3
) -> List[BrandSummary]:
    """
    Search for similar brands using vector similarity.

    Decision 5: Weaviate integration (adapter pattern).
    """
    return brand_repo.find_similar(brand, limit=limit)
```

**Location:** `app/use_cases/understand_brand_uc.py` (helper method)

**Dependencies:**
- `IBrandRepository` (Weaviate adapter): Vector search

---

#### 1.5 `accept_brand_choice(brand: BrandSummary, similar: List[BrandSummary]) -> Approval`

**Purpose:** HITL approval checkpoint for brand choice (Decision 9: Simulated)

**Implementation:**
```python
def accept_brand_choice(
    brand: BrandSummary,
    similar_brands: List[BrandSummary],
    brief_id: str
) -> Approval:
    """
    Log HITL approval checkpoint for brand choice.

    Decision 9: Simulated approval with logging.
    """
    approval = Approval(
        approval_id=f"approval-brand-{uuid.uuid4().hex[:8]}",
        brief_id=brief_id,
        checkpoint_name="brand_choice",
        status=ApprovalStatus.APPROVED,  # Auto-approved in PoC
        product_name=None,
        aspect_ratio=None,
        approved_by="system",
        comment=f"Brand: {brand.name}, Similar: {len(similar_brands)} found",
        started_at=datetime.now(),
        finished_at=datetime.now()
    )

    logging.info(f"[HITL CHECKPOINT] Brand Choice: {approval.comment}")

    return approval
```

**Location:** `app/use_cases/understand_brand_uc.py` (helper method)

**Dependencies:** None (creates entity)

---

### Phase 2: Localization

#### 2.1 `localize_campaign_slogans(slogan: str, locales: List[str], brand: BrandSummary) -> Dict[str, str]`

**Purpose:** Localize campaign slogan for all target locales (Decision 7)

**Implementation:**
```python
def localize_campaign_slogans(
    slogan: str,
    target_locales: List[str],
    brand: BrandSummary,
    locale_ai: IAIAdapter
) -> Dict[str, str]:
    """
    Localize campaign slogan using Claude Multilingual API.

    Decision 7: English + Spanish (US) for PoC.
    """
    localized = {}

    for locale in target_locales:
        if locale == "en-US":
            # English is base locale
            localized[locale] = slogan
        else:
            # Use Claude to localize
            prompt = f"""
            Translate this campaign slogan to {locale}, maintaining brand voice '{brand.voice_tone}'.
            Keep it concise (< 50 characters) and culturally appropriate.

            Original slogan: {slogan}
            """
            localized[locale] = locale_ai.generate_text(prompt).strip()

    return localized
```

**Location:** `app/use_cases/generate_campaign_uc.py` (helper method)

**Dependencies:**
- `IAIAdapter` (Claude Multilingual): Translation

---

### Phase 3: Asset Generation

#### 3.1 `search_existing_asset(product: str, aspect: str, locale: str) -> Optional[CreativeAsset]`

**Purpose:** Check if asset already exists for reuse (Decision 8)

**Implementation:**
```python
def search_existing_asset(
    product_name: str,
    aspect_ratio: str,
    locale: str,
    asset_repo: IAssetRepository
) -> Optional[CreativeAsset]:
    """
    Search for existing asset for reuse.

    Decision 8: Vector similarity search (deferred details to session-02).
    PoC: Simple filename convention matching.
    """
    return asset_repo.find_existing(
        product_name=product_name,
        aspect_ratio=aspect_ratio,
        locale=locale
    )
```

**Location:** `app/use_cases/generate_campaign_uc.py` (helper method)

**Dependencies:**
- `IAssetRepository`: Asset search (filesystem or Weaviate)

---

#### 3.2 `generate_hero_image(product: Product, brand: BrandSummary, aspect: str) -> Image`

**Purpose:** Generate hero image using OpenAI DALL-E 3 (Decision 3)

**Implementation:**
```python
def generate_hero_image(
    product: Product,
    brand: BrandSummary,
    aspect_ratio: str,
    image_ai: IAIAdapter
) -> bytes:
    """
    Generate hero image using OpenAI DALL-E 3.

    Decision 3: OpenAI DALL-E 3 for image generation.
    """
    # Build image prompt
    prompt = f"""
    Create a professional product photo of {product.name} for social media advertising.

    Style: {', '.join(product.palette_words)}
    Brand colors: {', '.join(brand.colors)}
    Brand voice: {brand.voice_tone}

    Aspect ratio: {aspect_ratio}
    High quality, photorealistic, commercial photography style.
    """

    # Generate image
    image_bytes = image_ai.generate_image(prompt, aspect_ratio=aspect_ratio)

    return image_bytes
```

**Location:** `app/use_cases/generate_campaign_uc.py` (helper method)

**Dependencies:**
- `IAIAdapter` (OpenAI Image): DALL-E 3 generation

---

#### 3.3 `add_slogan_to_image(image: Image, slogan: str, brand_colors: List[str]) -> Image`

**Purpose:** Add localized slogan to image using OpenAI API (Decision 3)

**Implementation:**
```python
def add_slogan_to_image(
    hero_image: bytes,
    slogan: str,
    brand_colors: List[str],
    text_ai: IAIAdapter
) -> bytes:
    """
    Add text overlay to hero image using OpenAI API.

    Decision 3: OpenAI API for text overlay.
    """
    # Use OpenAI API to add text to image
    # (In practice, may use PIL/Pillow for text overlay, or OpenAI edit API)

    final_image = text_ai.add_text_to_image(
        image=hero_image,
        text=slogan,
        color=brand_colors[0],  # Primary brand color
        position="bottom-center",
        font_size="large"
    )

    return final_image
```

**Location:** `app/use_cases/generate_campaign_uc.py` (helper method)

**Dependencies:**
- `IAIAdapter` (OpenAI Text): Text overlay (or PIL/Pillow)

---

#### 3.4 `save_creative_asset(asset: CreativeAsset, image: bytes) -> str`

**Purpose:** Save asset to organized folder structure (Decision 2)

**Implementation:**
```python
def save_creative_asset(
    asset: CreativeAsset,
    image_bytes: bytes,
    storage: IStorageAdapter
) -> str:
    """
    Save creative asset to organized folder structure.

    Decision 2: Local filesystem with adapter interface.
    PDF requirement: out/assets/{product}/{locale}/{aspect}/{asset_id}.png
    """
    file_path = asset.file_path(base_dir="out/assets")

    # Ensure directory exists
    os.makedirs(os.path.dirname(file_path), exist_ok=True)

    # Save via storage adapter
    storage.save(file_path, image_bytes)

    return file_path
```

**Location:** `app/use_cases/generate_campaign_uc.py` (helper method)

**Dependencies:**
- `IStorageAdapter` (Local/MinIO): File storage

---

### Phase 4: Validation

#### 4.1 `validate_brand_compliance(asset: CreativeAsset, brand: BrandSummary) -> ValidationResult`

**Purpose:** Check brand compliance (Decision 10: Stubbed)

**Implementation:**
```python
def validate_brand_compliance(
    asset: CreativeAsset,
    brand: BrandSummary,
    brand_validator: IBrandValidator
) -> ValidationResult:
    """
    Validate brand compliance (logo, colors).

    Decision 10: Stubbed for PoC.
    """
    # STUB: Always pass with warning
    return ValidationResult(
        asset_id=asset.asset_id,
        status=ValidationStatus.WARNING,
        checks_run=["brand_colors", "logo_presence"],
        issues=[
            ValidationIssue(
                check_name="brand_compliance",
                severity="warning",
                message="Brand compliance validation not implemented (stubbed)",
                fix_suggestion="Implement image analysis for colors and logo"
            )
        ],
        passed_count=1,
        failed_count=0
    )
```

**Location:** `app/use_cases/validate_campaign_uc.py` (helper method)

**Dependencies:**
- `IBrandValidator`: Brand compliance checks (stubbed)

---

#### 4.2 `check_legal_content(message: str) -> ValidationResult`

**Purpose:** Check for prohibited words (Decision 10: Implemented)

**Implementation:**
```python
PROHIBITED_WORDS = [
    "guaranteed", "free", "miracle", "cure", "approved",
    # ... more prohibited words
]

def check_legal_content(
    message: str,
    validation_adapter: IValidationAdapter
) -> ValidationResult:
    """
    Check message for prohibited words.

    Decision 10: Implemented for PoC.
    """
    prohibited_found = []

    message_lower = message.lower()
    for word in PROHIBITED_WORDS:
        if re.search(rf'\b{word}\b', message_lower):
            prohibited_found.append(word)

    if prohibited_found:
        return ValidationResult(
            asset_id="",
            status=ValidationStatus.FAILED,
            checks_run=["prohibited_words"],
            issues=[
                ValidationIssue(
                    check_name="prohibited_words",
                    severity="error",
                    message=f"Prohibited words found: {prohibited_found}",
                    fix_suggestion="Remove or replace prohibited words"
                )
            ],
            passed_count=0,
            failed_count=1
        )

    return ValidationResult(
        asset_id="",
        status=ValidationStatus.PASSED,
        checks_run=["prohibited_words"],
        issues=[],
        passed_count=1,
        failed_count=0
    )
```

**Location:** `app/use_cases/validate_campaign_uc.py` (helper method)

**Dependencies:**
- `IValidationAdapter`: Prohibited words list

---

#### 4.3 `aggregate_validation_results(results: List[ValidationResult]) -> ValidateCampaignResult`

**Purpose:** Aggregate validation results across all assets

**Implementation:** See `use-cases.md` → ValidateCampaignUseCase

**Location:** `app/use_cases/validate_campaign_uc.py`

---

### Phase 5: Approval & Output

#### 5.1 `accept_campaign_images(assets: List[CreativeAsset]) -> Approval`

**Purpose:** HITL approval checkpoint for generated assets (Decision 9: Simulated)

**Implementation:**
```python
def accept_campaign_images(
    assets: List[CreativeAsset],
    brief_id: str
) -> Approval:
    """
    Log HITL approval checkpoint for campaign assets.

    Decision 9: Simulated approval with logging.
    """
    approval = Approval(
        approval_id=f"approval-campaign-{uuid.uuid4().hex[:8]}",
        brief_id=brief_id,
        checkpoint_name="campaign_images",
        status=ApprovalStatus.APPROVED,  # Auto-approved in PoC
        product_name=None,
        aspect_ratio=None,
        approved_by="system",
        comment=f"Campaign complete: {len(assets)} assets generated",
        started_at=datetime.now(),
        finished_at=datetime.now()
    )

    logging.info(f"[HITL CHECKPOINT] Campaign Images: {approval.comment}")

    return approval
```

**Location:** `app/interface_adapters/orchestrators/campaign_orchestrator.py`

**Dependencies:** None (creates entity)

---

#### 5.2 `log_approval_checkpoint(checkpoint: str, data: dict) -> None`

**Purpose:** Structured logging for approval checkpoints (Decision 10)

**Implementation:**
```python
def log_approval_checkpoint(checkpoint_name: str, data: dict) -> None:
    """
    Log approval checkpoint with structured data.

    Decision 10: Logging feature implemented.
    Task 3: Foundation for agentic monitoring.
    """
    log_entry = {
        "timestamp": datetime.now().isoformat(),
        "checkpoint": checkpoint_name,
        "data": data
    }

    logging.info(f"[APPROVAL CHECKPOINT] {json.dumps(log_entry)}")

    # Optionally persist to database for Task 3
```

**Location:** `app/interface_adapters/orchestrators/campaign_orchestrator.py`

**Dependencies:** None (logging)

---

#### 5.3 `organize_output_folder(assets: List[CreativeAsset]) -> None`

**Purpose:** Organize assets into folder structure (PDF requirement)

**Implementation:**
```python
def organize_output_folder(assets: List[CreativeAsset]) -> None:
    """
    Ensure assets are organized in folder structure.

    PDF requirement: out/assets/{product}/{locale}/{aspect}/
    """
    for asset in assets:
        # Assets already saved with file_path() method
        # This function ensures directory structure exists
        dir_path = os.path.dirname(asset.image_url)
        os.makedirs(dir_path, exist_ok=True)
```

**Location:** `app/interface_adapters/orchestrators/campaign_orchestrator.py`

**Dependencies:** None (filesystem)

---

#### 5.4 `generate_campaign_report(metrics: dict, results: ValidateCampaignResult) -> str`

**Purpose:** Generate summary report (Decision 10: Logging feature)

**Implementation:**
```python
def generate_campaign_report(
    campaign_result: GenerateCampaignResult,
    validation_result: ValidateCampaignResult
) -> str:
    """
    Generate campaign summary report.

    Decision 10: Logging/reporting feature.
    """
    report = f"""
    Campaign Generation Report
    ==========================

    Total Assets: {len(campaign_result.assets)}
    - Generated: {campaign_result.generated_count}
    - Reused: {campaign_result.reused_count}
    - Failed: {campaign_result.failed_count}

    Generation Time: {campaign_result.generation_time_seconds:.2f}s

    Validation:
    - Passed: {validation_result.passed_count}
    - Failed: {validation_result.failed_count}
    - Warnings: {validation_result.warning_count}

    Status: {"✅ SUCCESS" if validation_result.success else "❌ FAILED"}
    """

    return report
```

**Location:** `app/interface_adapters/presenters/campaign_presenter.py`

**Dependencies:** None (formatting)

---

## Function Dependencies Graph

```
load_campaign_brief
    ↓
load_or_understand_brand
    ├─→ load_brand_from_repository (IBrandRepository)
    └─→ understand_brand (UnderstandBrandUseCase)
           ├─→ analyze_images (IAIAdapter - Claude Vision)
           └─→ find_similar (IBrandRepository - Weaviate)
                  ↓
         accept_brand_choice (HITL - simulated)
    ↓
localize_campaign_slogans
    └─→ generate_text (IAIAdapter - Claude Multilingual)
    ↓
[FOR EACH product × aspect × locale]
    ↓
search_existing_asset
    └─→ find_existing (IAssetRepository)
    ↓
[IF NOT FOUND]
    ↓
generate_hero_image
    └─→ generate_image (IAIAdapter - OpenAI DALL-E 3)
    ↓
add_slogan_to_image
    └─→ add_text_to_image (IAIAdapter - OpenAI Text)
    ↓
save_creative_asset
    └─→ save (IStorageAdapter - Local/MinIO)
    ↓
[END FOR EACH]
    ↓
validate_brand_compliance (STUBBED)
    └─→ check_compliance (IBrandValidator)
    ↓
check_legal_content (IMPLEMENTED)
    └─→ check_prohibited_words (IValidationAdapter)
    ↓
aggregate_validation_results
    ↓
accept_campaign_images (HITL - simulated)
    ↓
log_approval_checkpoint (Logging)
    ↓
organize_output_folder
    ↓
generate_campaign_report
```

---

## Error Handling Flow

```
┌─────────────────────────────────────────────────────────────┐
│ Error Type: FileNotFoundError (Campaign Brief)              │
├─────────────────────────────────────────────────────────────┤
│ Handler: load_campaign_brief()                              │
│ Action: Raise exception, exit workflow                      │
│ User Message: "Campaign brief not found: {path}"            │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Error Type: AIAdapterError (OpenAI API)                     │
├─────────────────────────────────────────────────────────────┤
│ Handler: generate_hero_image()                              │
│ Action: Retry with exponential backoff (max 3 attempts)     │
│ Fallback: Skip asset, increment failed_count                │
│ Alert: Create ALERT_TYPE.GENERATION_FAILED (Task 3)         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Error Type: ValidationError (Prohibited Words)              │
├─────────────────────────────────────────────────────────────┤
│ Handler: check_legal_content()                              │
│ Action: Mark asset as FAILED                                │
│ Alert: Create ALERT_TYPE.VALIDATION_FAILED (Task 3)         │
│ User Message: "Prohibited words found: {words}"             │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Error Type: StorageError (File Write)                       │
├─────────────────────────────────────────────────────────────┤
│ Handler: save_creative_asset()                              │
│ Action: Retry once, then fail                               │
│ Fallback: Log error, continue with next asset               │
│ Alert: Create ALERT_TYPE.STORAGE_ERROR (Task 3)             │
└─────────────────────────────────────────────────────────────┘
```

---

## Adapter Interfaces

### IAIAdapter

```python
class IAIAdapter(ABC):
    """Protocol for AI service adapters."""

    @abstractmethod
    def analyze_images(self, images: List[bytes], prompt: str) -> dict:
        """Analyze images (Claude Vision)."""
        pass

    @abstractmethod
    def generate_image(self, prompt: str, aspect_ratio: str) -> bytes:
        """Generate image (OpenAI DALL-E 3)."""
        pass

    @abstractmethod
    def generate_text(self, prompt: str) -> str:
        """Generate text (Claude)."""
        pass

    @abstractmethod
    def add_text_to_image(self, image: bytes, text: str, color: str) -> bytes:
        """Add text overlay (OpenAI or PIL)."""
        pass
```

### IStorageAdapter

```python
class IStorageAdapter(ABC):
    """Protocol for storage adapters."""

    @abstractmethod
    def save(self, path: str, content: bytes) -> str:
        """Save content to storage."""
        pass

    @abstractmethod
    def load(self, path: str) -> bytes:
        """Load content from storage."""
        pass

    @abstractmethod
    def exists(self, path: str) -> bool:
        """Check if file exists."""
        pass
```

### IBrandRepository

```python
class IBrandRepository(ABC):
    """Protocol for brand repository."""

    @abstractmethod
    def get_by_id(self, brand_id: str) -> Optional[BrandSummary]:
        """Load brand by ID."""
        pass

    @abstractmethod
    def find_similar(self, brand: BrandSummary, limit: int) -> List[BrandSummary]:
        """Find similar brands (Weaviate)."""
        pass

    @abstractmethod
    def save(self, brand: BrandSummary) -> None:
        """Save brand."""
        pass
```

### IAssetRepository

```python
class IAssetRepository(ABC):
    """Protocol for asset repository."""

    @abstractmethod
    def find_existing(self, product_name: str, aspect_ratio: str, locale: str) -> Optional[CreativeAsset]:
        """Find existing asset for reuse."""
        pass

    @abstractmethod
    def save(self, asset: CreativeAsset) -> None:
        """Save asset metadata."""
        pass
```

---

## Task 3 Extensions: Monitoring Hooks

**Alert Generation Points:**

1. **Brand Understanding:**
   - Confidence < 0.5 → `ALERT_TYPE.INSUFFICIENT_CONFIDENCE`

2. **Asset Generation:**
   - Failed count > 0 → `ALERT_TYPE.GENERATION_FAILED`
   - Generated < expected → `ALERT_TYPE.INSUFFICIENT_VARIANTS`

3. **Validation:**
   - Failed count > 0 → `ALERT_TYPE.VALIDATION_FAILED`

4. **Performance:**
   - Generation time > threshold → `ALERT_TYPE.PERFORMANCE_DEGRADATION`

**Model Context Protocol Integration:**

```python
def create_alert_with_context(
    alert_type: AlertType,
    brief_id: str,
    context: dict
) -> Alert:
    """
    Create alert with context for Model Context Protocol.

    Task 3: LLM uses context to generate stakeholder email.
    """
    alert = Alert(
        alert_id=f"alert-{uuid.uuid4().hex[:8]}",
        brief_id=brief_id,
        alert_type=alert_type,
        severity=_determine_severity(alert_type),
        message=_generate_message(alert_type, context),
        context=context,
        created_at=datetime.now(),
        resolved_at=None,
        resolution=None
    )

    # Task 3: Send to LLM for stakeholder communication
    # stakeholder_email = llm.generate_email(alert)

    return alert
```

---

## Next Steps

1. Implement orchestrator in `app/interface_adapters/orchestrators/campaign_orchestrator.py`
2. Implement adapter protocols in `app/adapters/*/protocol.py`
3. Implement fake adapters for testing in `app/adapters/*/fake.py`
4. Wire dependencies in CLI driver `drivers/cli/commands.py`
5. Create one feature test in `tests/features/test_localized_campaign.py`

**Related Documents:**
- `use-cases.md`: Detailed use case specifications
- `architecture-diagram.md`: Layer interactions and boundaries
- `entities.md`: Domain model definitions
