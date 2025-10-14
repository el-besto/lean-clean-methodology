# Campaign Generator: Domain Entities

**Status:** Draft for Implementation
**Based On:** Resolved architectural decisions (13/13)
**Source:** User ideation + PDF requirements + Lean-Clean patterns

---

## Overview

This document defines the core domain entities for the Campaign Generator PoC. These entities follow **Pragmatic Clean Architecture** patterns (Decision 1) and support the requirements from Tasks 1-3.

---

## Core Domain Entities

### 1. BrandSummary

**Purpose:** Encapsulates brand identity, guidelines, and consistency rules

**Location:** `app/entities/brand_summary.py`

```python
from dataclasses import dataclass
from typing import List, Optional
from datetime import datetime

@dataclass
class BrandSummary:
    """
    Brand identity and guidelines for campaign generation.

    Generated via Claude Vision API from brand assets.
    Stored in YAML config (Decision 5) with adapter for Weaviate evolution.
    """
    brand_id: str
    name: str
    description: str
    colors: List[str]  # Hex codes: ["#FF5733", "#33FF57"]
    typography: Optional[str]
    voice_tone: str  # e.g., "professional", "playful", "authoritative"
    target_audiences: List[str]
    target_regions: List[str]
    products: List[str]
    campaign_slogans: List[str]  # Historical slogans for consistency
    logo_url: Optional[str]
    created_at: datetime
    updated_at: datetime

    def validate(self) -> bool:
        """Validate brand summary has minimum required fields."""
        return bool(
            self.brand_id and
            self.name and
            self.description and
            self.colors and
            self.voice_tone
        )
```

**Business Rules:**
- Brand ID must be unique
- At least one color must be defined
- Voice tone must be consistent across campaigns
- Historical slogans prevent duplication

---

### 2. CampaignBrief

**Purpose:** Input contract for campaign generation (Decision 4: YAML format)

**Location:** `app/entities/campaign_brief.py`

```python
from dataclasses import dataclass
from typing import List
from datetime import datetime

@dataclass
class Product:
    """Product to be featured in campaign."""
    name: str
    palette_words: List[str]  # Visual style keywords: ["vibrant", "modern"]

    def __post_init__(self):
        if not self.name:
            raise ValueError("Product name is required")

@dataclass
class CampaignBrief:
    """
    Campaign brief input (Decision 4: YAML format).

    Defines what campaign to generate, for whom, and in which locales.
    Decision 6: Locale handling at campaign level (not product level).
    """
    brief_id: str
    brand_id: str
    campaign_slogan: str
    target_region: str  # e.g., "North America", "EMEA"
    target_audience: str  # e.g., "Gen Z professionals", "Parents 35-50"
    target_locales: List[str]  # Decision 7: ["en-US", "es-US"]
    products: List[Product]  # Decision: Minimum 2 products (PDF requirement)
    aspects: List[str]  # Decision: Minimum 3 ratios: ["1:1", "9:16", "16:9"]
    created_at: datetime

    def validate(self) -> bool:
        """Validate campaign brief meets PDF requirements."""
        return bool(
            self.brief_id and
            self.brand_id and
            self.campaign_slogan and
            len(self.products) >= 2 and  # PDF: at least 2 products
            len(self.aspects) >= 3 and   # PDF: at least 3 aspect ratios
            len(self.target_locales) >= 1  # At least English (Decision 7)
        )

    @property
    def total_assets_required(self) -> int:
        """Calculate total number of creative assets to generate."""
        return len(self.products) * len(self.aspects) * len(self.target_locales)
```

**Business Rules:**
- Minimum 2 products (PDF requirement)
- Minimum 3 aspect ratios (PDF requirement)
- Minimum 1 locale (English), bonus for 2+ (English + Spanish)
- Brief ID must be unique

---

### 3. CreativeAsset

**Purpose:** Generated campaign creative (image + message)

**Location:** `app/entities/creative_asset.py`

```python
from dataclasses import dataclass
from typing import Optional
from datetime import datetime

@dataclass
class CreativeAsset:
    """
    Generated creative asset for social ad campaign.

    Combines:
    - Hero image (generated via OpenAI DALL-E 3 or reused via Decision 8)
    - Localized campaign message (via Claude multilingual API)
    - Text overlay (via OpenAI API)
    """
    asset_id: str
    brief_id: str
    brand_id: str
    product_name: str
    audience: str
    locale: str  # e.g., "en-US", "es-US"
    aspect_ratio: str  # "1:1", "9:16", "16:9"
    message: str  # Localized campaign slogan
    image_url: str  # Path or URL to generated image
    reused: bool  # Decision 8: Was asset reused from existing?
    generated_at: datetime
    meta: dict  # Additional metadata (GenAI params, validation results, etc.)

    def file_path(self, base_dir: str = "out/assets") -> str:
        """
        Generate organized file path (PDF requirement).

        Example: out/assets/product-soap/en-US/1x1/asset-123.png
        """
        return f"{base_dir}/{self.product_name}/{self.locale}/{self.aspect_ratio}/{self.asset_id}.png"

    @property
    def display_name(self) -> str:
        """Human-readable asset identifier."""
        return f"{self.product_name}_{self.aspect_ratio}_{self.locale}"
```

**Business Rules:**
- Asset ID must be unique
- File organization: `{product}/{locale}/{aspect_ratio}/` (PDF requirement)
- Image URL can be local path or remote URL (Decision 2: storage adapter)
- Meta stores GenAI parameters for reproducibility

---

### 4. Approval

**Purpose:** HITL approval tracking (Task 3: Agentic system)

**Location:** `app/entities/approval.py`

```python
from dataclasses import dataclass
from typing import Optional
from datetime import datetime
from enum import Enum

class ApprovalStatus(Enum):
    """Approval workflow states."""
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"
    IN_REVIEW = "in_review"

@dataclass
class Approval:
    """
    HITL approval checkpoint (Decision 9: Simulated with logging).

    Tracks approval workflow for:
    - Brand choice (understand_brand → search_similar_brands → accept_brand_choice)
    - Campaign images (generate_campaign → accept_campaign_images)
    """
    approval_id: str
    brief_id: str
    checkpoint_name: str  # e.g., "brand_choice", "campaign_images"
    status: ApprovalStatus
    product_name: Optional[str]
    aspect_ratio: Optional[str]
    approved_by: Optional[str]  # Stakeholder name (for Task 3)
    comment: str
    started_at: datetime
    finished_at: Optional[datetime]

    @property
    def duration_seconds(self) -> Optional[int]:
        """Calculate approval duration (for Task 3 metrics)."""
        if self.finished_at:
            return int((self.finished_at - self.started_at).total_seconds())
        return None

    def is_blocking(self) -> bool:
        """Check if approval is blocking campaign generation."""
        return self.status in [ApprovalStatus.PENDING, ApprovalStatus.IN_REVIEW]
```

**Business Rules:**
- Approval required at key checkpoints (Decision 9: simulated)
- Status transitions: PENDING → IN_REVIEW → APPROVED/REJECTED
- Tracks stakeholder and timing for Task 3 analysis

---

### 5. Alert

**Purpose:** Agentic monitoring and alerting (Task 3)

**Location:** `app/entities/alert.py`

```python
from dataclasses import dataclass
from datetime import datetime
from enum import Enum

class AlertSeverity(Enum):
    """Alert severity levels."""
    INFO = "info"
    WARNING = "warning"
    ERROR = "error"
    CRITICAL = "critical"

class AlertType(Enum):
    """Types of alerts for agentic monitoring."""
    MISSING_ASSETS = "missing_assets"
    INSUFFICIENT_VARIANTS = "insufficient_variants"  # < 3 variants (Task 3 requirement)
    GENERATION_FAILED = "generation_failed"
    VALIDATION_FAILED = "validation_failed"
    API_ERROR = "api_error"
    APPROVAL_TIMEOUT = "approval_timeout"

@dataclass
class Alert:
    """
    Alert for agentic monitoring system (Task 3).

    Tracks issues requiring:
    - Automated remediation
    - Human notification
    - Stakeholder communication
    """
    alert_id: str
    brief_id: str
    alert_type: AlertType
    severity: AlertSeverity
    message: str
    context: dict  # Additional context for Model Context Protocol (Task 3)
    created_at: datetime
    resolved_at: Optional[datetime]
    resolution: Optional[str]

    def to_human_readable(self) -> str:
        """
        Format alert for stakeholder communication (Task 3).

        Uses Model Context Protocol to generate human-readable message
        via LLM (e.g., Claude).
        """
        return f"[{self.severity.value.upper()}] {self.alert_type.value}: {self.message}"

    @property
    def is_resolved(self) -> bool:
        """Check if alert has been resolved."""
        return self.resolved_at is not None
```

**Business Rules:**
- Alert created when monitoring detects issue
- Severity determines escalation path
- Context provides info for Model Context Protocol (Task 3)
- Resolution tracked for learning and optimization (Business Goal 5)

---

### 6. ValidationResult

**Purpose:** Brand compliance and legal checks (Decision 10: Nice-to-have)

**Location:** `app/entities/validation_result.py`

```python
from dataclasses import dataclass
from typing import List
from enum import Enum

class ValidationStatus(Enum):
    """Validation check status."""
    PASSED = "passed"
    FAILED = "failed"
    WARNING = "warning"
    SKIPPED = "skipped"

@dataclass
class ValidationIssue:
    """Individual validation issue."""
    check_name: str
    severity: str  # "error", "warning", "info"
    message: str
    fix_suggestion: Optional[str]

@dataclass
class ValidationResult:
    """
    Validation result for brand compliance and legal checks.

    Decision 10: Implement logging + legal checks (stubbed orchestrator methods).
    - Brand compliance: Logo presence, color usage (stubbed for PoC)
    - Legal checks: Prohibited words (implemented)
    """
    asset_id: str
    status: ValidationStatus
    checks_run: List[str]  # ["prohibited_words", "brand_colors", "logo_presence"]
    issues: List[ValidationIssue]
    passed_count: int
    failed_count: int

    @property
    def is_valid(self) -> bool:
        """Check if validation passed (no errors)."""
        return self.status == ValidationStatus.PASSED and self.failed_count == 0

    def summary(self) -> str:
        """Generate validation summary for logging."""
        return f"Validation: {self.passed_count} passed, {self.failed_count} failed"
```

**Business Rules:**
- All assets must pass legal checks before approval
- Brand compliance warnings don't block but are logged
- Failed validation creates Alert (Task 3)

---

## Entity Relationships

```
CampaignBrief (input)
    ├─→ BrandSummary (loaded via brand_id)
    │   └─→ Colors, voice tone, historical slogans
    │
    ├─→ Products (embedded list)
    │   └─→ Product names, palette words
    │
    └─→ CreativeAsset (generated output)
        ├─→ Image URL (from OpenAI DALL-E 3 or reused)
        ├─→ Localized message (from Claude multilingual)
        ├─→ ValidationResult (brand + legal checks)
        ├─→ Approval (HITL checkpoints)
        └─→ Alert (if generation/validation fails)
```

---

## Usage in PoC

### Minimum PoC Flow (Task 2)

1. **Input:** Load `CampaignBrief` from YAML
2. **Brand:** Load `BrandSummary` from YAML config (or Weaviate)
3. **Generate:** Create `CreativeAsset` for each product × aspect ratio × locale
4. **Validate:** Run `ValidationResult` checks (legal words)
5. **Log:** Create `Approval` checkpoints (simulated)
6. **Output:** Save `CreativeAsset` to organized folder structure

### Agentic System (Task 3)

7. **Monitor:** Create `Alert` when:
   - Fewer than 3 variants generated
   - Validation fails
   - API errors occur
8. **Communicate:** Use Model Context Protocol to draft stakeholder emails

---

## Next Steps

- Implement entities in `app/entities/` folder
- Create corresponding YAML schemas for `CampaignBrief` and `BrandSummary`
- Define use cases that orchestrate entity interactions
- Create fakes/in-memory versions for testing (Lean-Clean pattern)
