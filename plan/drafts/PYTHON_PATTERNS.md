# Python Patterns & Idioms for Lean-Clean PoCs

## Summary

This document identifies Python-specific patterns from the **lean-clean-python-style-guide** that should be incorporated into the proposed PoC structure. These patterns enhance type safety, testability, and maintainability while keeping code lean and composable.

## Key Pattern Updates Needed in STRUCTURE.md

### 1. Dataclasses with `slots=True, frozen=True`

**Current pattern in STRUCTURE.md:**
```python
# domain/models.py
from dataclasses import dataclass

@dataclass
class Brief:
    brand_id: str
    campaign_message: str
    products: List['Product']
```

**Recommended pattern:**
```python
# domain/models.py
from dataclasses import dataclass

@dataclass(slots=True, frozen=True)
class Brief:
    """Immutable brief for campaign generation.

    Using slots=True reduces memory footprint.
    Using frozen=True makes instances immutable and hashable.
    """
    brand_id: str
    campaign_message: str
    products: tuple['Product', ...]  # Use tuple for immutability
```

**Why:** Immutable domain objects prevent accidental mutations, are hashable (can be dict keys or set members), and use less memory.

### 2. Protocol Ports with Proper Type Annotations

**Current pattern in STRUCTURE.md:**
```python
# application/ports/generation.py
from typing import Protocol

class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...
```

**Recommended pattern:**
```python
# application/ports/generation.py
from typing import Protocol, Iterable, Sequence

class ImageGenPort(Protocol):
    """Port for image generation services.

    Use-cases depend on this protocol without importing vendor SDKs.
    Implementations live in infrastructure/adapters/.
    """
    def generate_png(self, prompt: str, size: str) -> bytes:
        """Generate a PNG image from a text prompt.

        Args:
            prompt: Text description of the image to generate.
            size: Image dimensions (e.g., "1024x1024").

        Returns:
            PNG image data as bytes.
        """
        ...

class VectorStorePort(Protocol):
    """Port for vector storage and retrieval."""

    def embed(self, texts: Iterable[str]) -> Sequence[Sequence[float]]:
        """Embed text strings into vector representations.

        Args:
            texts: Iterable of strings to embed (use Iterable for flexibility).

        Returns:
            Sequence of vector embeddings (use Sequence for list-like behavior).
        """
        ...
```

**Why:**
- Use `Iterable` for input parameters (accepts lists, tuples, generators)
- Use `Sequence` for return types (guarantees indexing and length)
- Docstrings clarify intent and layer boundaries

### 3. TypedDict for Payload Composition

**Pattern to add:**
```python
# infrastructure/adapters/generation/openai_payload.py
from typing import TypedDict, NotRequired

class InvokeParams(TypedDict, total=False):
    """Invocation parameters for OpenAI image generation.

    Keys are optional so callers specify only what they need.
    TypedDict enables static type checkers to warn about unsupported keys.
    """
    temperature: float
    top_p: float
    n: int
    user: NotRequired[str]  # Explicitly optional

def build_openai_payload(
    model: str,
    prompt: str,
    size: str,
    params: InvokeParams
) -> dict:
    """Compose an OpenAI request body by merging parameters.

    Later keys override earlier ones; unknown keys cause type errors.
    """
    return {
        "model": model,
        "prompt": prompt,
        **params,  # Merge invocation parameters
        "size": size,  # Size comes last to ensure it's not overridden
    }
```

**Why:** TypedDict provides static type checking for dictionary merges, catching typos and invalid keys at development time.

### 4. Singledispatch for Response Normalization

**Pattern to add:**
```python
# infrastructure/adapters/generation/normalize.py
from functools import singledispatch
from dataclasses import dataclass

@dataclass(slots=True, frozen=True)
class ImageGenResult:
    """Normalized result from any image generation provider."""
    image_data: bytes
    prompt_used: str
    revised_prompt: str | None
    content_filter_applied: bool

@singledispatch
def normalize_image_response(response: object) -> ImageGenResult:
    """Default normalizer for unknown response types.

    Raises NotImplementedError for unregistered types.
    """
    raise NotImplementedError(f"Unknown response type: {type(response)}")

# Register OpenAI-specific normalizer
@normalize_image_response.register
def _(response: "OpenAIImageResponse") -> ImageGenResult:
    """Convert OpenAI response to domain result."""
    return ImageGenResult(
        image_data=response.data[0].b64_json.decode(),
        prompt_used=response.data[0].prompt,
        revised_prompt=response.data[0].revised_prompt,
        content_filter_applied=response.data[0].content_filter_results is not None,
    )

# Register Stability AI-specific normalizer
@normalize_image_response.register
def _(response: "StabilityImageResponse") -> ImageGenResult:
    """Convert Stability AI response to domain result."""
    return ImageGenResult(
        image_data=response.artifacts[0].binary,
        prompt_used=response.prompt,
        revised_prompt=None,
        content_filter_applied=response.artifacts[0].finish_reason == "CONTENT_FILTERED",
    )
```

**Why:** Singledispatch centralizes vendor response normalization, making it easy to add new providers without modifying existing code.

### 5. Attribute Flattening for Observability

**Pattern to add:**
```python
# infrastructure/telemetry/attributes.py
from typing import Iterator
from domain.models import Brief, Asset

def span_kind_attrs() -> Iterator[tuple[str, str]]:
    """Yield span kind attribute for internal operations."""
    yield ("span.kind", "internal")

def brief_attrs(brief: Brief) -> Iterator[tuple[str, str]]:
    """Yield attributes derived from a Brief domain object."""
    yield ("campaign.brand_id", brief.brand_id)
    yield ("campaign.product_count", str(len(brief.products)))
    yield ("campaign.message", brief.campaign_message)

def generation_attrs(prompt: str, model: str) -> Iterator[tuple[str, str]]:
    """Yield attributes for image generation."""
    yield ("llm.model_name", model)
    yield ("llm.prompt_length", str(len(prompt)))

def asset_attrs(asset: Asset) -> Iterator[tuple[str, str]]:
    """Yield attributes from generated asset."""
    yield ("asset.product_id", asset.product_id)
    yield ("asset.aspect", asset.aspect)
    yield ("asset.reused", str(asset.reused))

# Usage in presenter or use case:
from itertools import chain

def record_generation_telemetry(
    span,
    brief: Brief,
    prompt: str,
    model: str,
    asset: Asset
):
    """Attach telemetry attributes to a tracing span.

    Flatten multiple attribute generators inline.
    """
    for key, value in chain(
        span_kind_attrs(),
        brief_attrs(brief),
        generation_attrs(prompt, model),
        asset_attrs(asset),
    ):
        span.set_attribute(key, value)
```

**Alternative inline flattening with `*` operator:**
```python
def record_generation_telemetry(span, brief: Brief, prompt: str, model: str, asset: Asset):
    """Attach telemetry using * operator for flattening."""
    for key, value in (
        *span_kind_attrs(),
        *brief_attrs(brief),
        *generation_attrs(prompt, model),
        *asset_attrs(asset),
    ):
        span.set_attribute(key, value)
```

**Why:**
- Small, pure, testable functions
- No intermediate list construction
- Easy to compose and reuse
- Derives telemetry from domain objects, not vendor payloads

### 6. Error Taxonomy

**Pattern to add:**
```python
# domain/exceptions.py

class AppError(Exception):
    """Base class for all application errors.

    Derive domain-specific errors from this base.
    Never raise vendor-specific exceptions from domain/application layers.
    """
    pass

class ValidationError(AppError):
    """Raised when input validation fails."""
    pass

class ResourceNotFoundError(AppError):
    """Raised when a requested resource doesn't exist."""
    pass

class VendorError(AppError):
    """Raised when an external service fails.

    Gateways should catch vendor exceptions and raise this.
    """
    def __init__(self, message: str, vendor_error: Exception | None = None):
        super().__init__(message)
        self.vendor_error = vendor_error

class RateLimitError(VendorError):
    """Raised when hitting API rate limits."""
    pass

# Example usage in gateway:
# infrastructure/adapters/generation/openai_image_gen.py
from openai import OpenAIError, RateLimitError as OpenAIRateLimit
from domain.exceptions import VendorError, RateLimitError

class OpenAIImageGen:
    def generate_png(self, prompt: str, size: str) -> bytes:
        try:
            response = self.client.images.generate(...)
            return response.data[0].b64_json
        except OpenAIRateLimit as e:
            # Translate vendor exception to domain exception
            raise RateLimitError(f"OpenAI rate limit exceeded: {e}", vendor_error=e)
        except OpenAIError as e:
            raise VendorError(f"OpenAI API error: {e}", vendor_error=e)

# Example usage in driver (error translation):
# drivers/api/main.py
from fastapi import HTTPException
from domain.exceptions import ValidationError, ResourceNotFoundError, VendorError, RateLimitError

@app.post("/generate")
async def generate_creative(request: CreativeRequest):
    try:
        result = use_case.execute(...)
        return result
    except ValidationError as e:
        raise HTTPException(status_code=422, detail=str(e))
    except ResourceNotFoundError as e:
        raise HTTPException(status_code=404, detail=str(e))
    except RateLimitError as e:
        raise HTTPException(status_code=429, detail=str(e))
    except VendorError as e:
        raise HTTPException(status_code=502, detail="External service error")
    except AppError as e:
        raise HTTPException(status_code=500, detail=str(e))
```

**Why:**
- Domain exceptions are independent of frameworks
- Easy to test error handling
- Consistent error translation at edges
- Vendor errors don't leak into domain

### 7. Config with LRU Cache

**Pattern to add:**
```python
# infrastructure/config/settings.py
from functools import lru_cache
import os
from dataclasses import dataclass

@dataclass(frozen=True)
class Settings:
    """Application configuration loaded from environment.

    Immutable settings object cached per process.
    """
    # API Keys
    openai_api_key: str
    arize_api_key: str | None

    # Infrastructure
    weaviate_url: str
    postgres_url: str
    s3_bucket: str

    # Observability
    otel_endpoint: str
    phoenix_endpoint: str

    # Feature flags
    enable_observability: bool

@lru_cache(maxsize=1)
def get_settings() -> Settings:
    """Load settings from environment once per process.

    The @lru_cache decorator ensures this only runs once.
    Override in tests by calling get_settings.cache_clear().
    """
    return Settings(
        openai_api_key=os.environ["OPENAI_API_KEY"],
        arize_api_key=os.environ.get("ARIZE_API_KEY"),
        weaviate_url=os.environ.get("WEAVIATE_URL", "http://localhost:8080"),
        postgres_url=os.environ.get("POSTGRES_URL", "postgresql://localhost/pocdb"),
        s3_bucket=os.environ.get("S3_BUCKET", "poc-assets"),
        otel_endpoint=os.environ.get("OTEL_ENDPOINT", "http://localhost:4317"),
        phoenix_endpoint=os.environ.get("PHOENIX_ENDPOINT", "http://localhost:6006"),
        enable_observability=os.environ.get("ENABLE_OBSERVABILITY", "false").lower() == "true",
    )

# Usage in drivers:
# drivers/cli/__main__.py
from infrastructure.config.settings import get_settings

def build_dependencies():
    settings = get_settings()
    return {
        "image_gen": OpenAIImageGen(api_key=settings.openai_api_key),
        "vector_store": WeaviateAdapter(url=settings.weaviate_url),
        # ...
    }
```

**Why:**
- Settings loaded once per process (efficient)
- Easy to mock in tests (`get_settings.cache_clear()`)
- Type-safe configuration
- Explicit about required vs optional settings

### 8. DTOs vs Domain Separation

**Pattern to add:**
```python
# drivers/api/schemas.py (Pydantic models at the edge)
from pydantic import BaseModel, Field

class ProductPayload(BaseModel):
    """Inbound product data from API request."""
    id: str
    name: str
    palette_words: list[str] = Field(default_factory=list)

class BriefPayload(BaseModel):
    """Inbound brief data from API request."""
    brand_id: str
    campaign_message: str
    products: list[ProductPayload]
    aspects: list[str] = Field(default_factory=lambda: ["1:1", "9:16", "16:9"])

class AssetResponse(BaseModel):
    """Outbound asset data for API response."""
    product_id: str
    aspect: str
    image_url: str
    reused: bool

# interface_adapters/controllers/creative_controller.py (Conversion layer)
from domain.models import Brief, Product
from drivers.api.schemas import BriefPayload, AssetResponse

def payload_to_brief(payload: BriefPayload) -> Brief:
    """Convert API payload to domain Brief.

    This conversion happens exactly once, at the edge.
    Domain layer never imports Pydantic.
    """
    products = tuple(
        Product(
            id=p.id,
            name=p.name,
            palette_words=tuple(p.palette_words)
        )
        for p in payload.products
    )

    return Brief(
        brand_id=payload.brand_id,
        campaign_message=payload.campaign_message,
        products=products,
        aspects=tuple(payload.aspects),
    )

def asset_to_response(asset: Asset) -> AssetResponse:
    """Convert domain Asset to API response."""
    return AssetResponse(
        product_id=asset.product_id,
        aspect=asset.aspect,
        image_url=asset.image_url,
        reused=asset.reused,
    )

# drivers/api/routers/creative_router.py (Usage in controller)
from fastapi import APIRouter, HTTPException
from drivers.api.schemas import BriefPayload, AssetResponse
from interface_adapters.controllers.creative_controller import payload_to_brief, asset_to_response

router = APIRouter()

@router.post("/generate", response_model=list[AssetResponse])
async def generate_creatives(payload: BriefPayload):
    """Generate creative assets from a campaign brief.

    Controller responsibilities:
    1. Validate payload (Pydantic does this)
    2. Convert to domain objects
    3. Invoke use case
    4. Handle errors
    5. Convert results to response
    """
    try:
        # Convert once at the boundary
        brief = payload_to_brief(payload)

        # Invoke use case with domain objects
        assets = generate_creatives_use_case(brief, ...)

        # Convert results to API response
        return [asset_to_response(a) for a in assets]

    except ValidationError as e:
        raise HTTPException(status_code=422, detail=str(e))
```

**Why:**
- Pydantic models stay at the edge (drivers layer)
- Domain layer remains framework-independent
- API changes don't ripple into business logic
- Clear conversion boundaries

### 9. Functional Utilities

**Pattern to add:**
```python
# infrastructure/utils/helpers.py
import json
import re
from functools import lru_cache
from pathlib import Path

def to_json_safe(value: object) -> str:
    """Convert any object to JSON string safely.

    Falls back to str() for non-serializable types.
    Use for logging and telemetry attributes.
    """
    try:
        return json.dumps(value, default=str)
    except Exception:
        return "{}"

@lru_cache(maxsize=128)
def compile_pattern(pattern: str) -> re.Pattern:
    """Compile and cache a regex pattern.

    Use this instead of re.compile() at every call site.
    """
    return re.compile(pattern)

def ensure_path(path: str | Path) -> Path:
    """Ensure path exists, creating parent directories if needed.

    Returns Path object for chaining operations.
    """
    p = Path(path) if isinstance(path, str) else path
    p.parent.mkdir(parents=True, exist_ok=True)
    return p

# Usage examples:
from infrastructure.utils.helpers import to_json_safe, ensure_path

# In telemetry:
span.set_attribute("request.payload", to_json_safe(payload))

# In file operations:
output_path = ensure_path("./out/campaign/product-1/creative.png")
output_path.write_bytes(image_data)
```

### 10. Context Variables for Request Tracing

**Pattern to add (optional, for advanced use cases):**
```python
# infrastructure/telemetry/context.py
import contextvars
from contextlib import contextmanager

# Context variable for request ID
request_id_var = contextvars.ContextVar("request_id", default="-")

@contextmanager
def with_request_id(request_id: str):
    """Set request ID for the current async context.

    All code within this context can access the request ID
    without passing it through every function signature.
    """
    token = request_id_var.set(request_id)
    try:
        yield
    finally:
        request_id_var.reset(token)

def get_request_id() -> str:
    """Get current request ID from context."""
    return request_id_var.get()

# Usage in driver:
# drivers/api/middleware.py
from infrastructure.telemetry.context import with_request_id, get_request_id
import uuid

@app.middleware("http")
async def add_request_id(request: Request, call_next):
    request_id = str(uuid.uuid4())
    with with_request_id(request_id):
        response = await call_next(request)
        response.headers["X-Request-ID"] = request_id
        return response

# Usage in logging/telemetry (anywhere in the call stack):
from infrastructure.telemetry.context import get_request_id

def log_event(message: str):
    print(f"[{get_request_id()}] {message}")
```

## Summary of Changes to STRUCTURE.md

### High Priority Updates

1. **Add `slots=True, frozen=True` to all domain dataclass examples**
2. **Add TypedDict pattern for payload composition** (in adapters section)
3. **Add singledispatch normalization pattern** (in adapters section)
4. **Add attribute flattening pattern** (in observability section)
5. **Add error taxonomy section** with translation examples
6. **Update config pattern** to use `@lru_cache`
7. **Add DTOs vs Domain conversion examples** (controllers section)

### Medium Priority Updates

8. Add more complete docstrings to protocol examples
9. Add `Iterable`/`Sequence` typing guidance
10. Add functional utilities section
11. Add context variables pattern (optional)

### Low Priority Updates

12. Add snapshot testing examples (mentioned in style guide but not detailed)
13. Add `ExitStack` pattern for composing context managers
14. Add `functools.partial` pattern for binding parameters

## Implementation Checklist

When updating STRUCTURE.md:

- [ ] Update all domain dataclass examples with `slots=True, frozen=True`
- [ ] Add TypedDict section to "Key Architectural Patterns"
- [ ] Add singledispatch section to "Key Architectural Patterns"
- [ ] Add attribute flattening section to observability patterns
- [ ] Expand error handling section with taxonomy and translation
- [ ] Update config pattern to use `@lru_cache`
- [ ] Add DTO conversion examples to Interface Adapters section
- [ ] Add reference to lean-clean-python-style-guide in "References" section

## References

- **Source:** `../lean-clean-code/lean-clean-python-style-guide`
- **Key Documents:**
  - `SUMMARY.md` - Overview of all patterns
  - `PART-A.Python-General/README.md` - Core Python idioms
  - `PART-B.Python-with-LLMs/README.md` - LLM-specific patterns
  - `PART-C.Clean-Architecture/README.md` - Layer placement guide
  - `applied/*.py` - Canonical code examples
  - `CURSOR-and-Snippets/snippets/*.py` - Ready-to-use snippets

---

**Status:** Ready for review and integration into STRUCTURE.md
**Next Steps:**
1. Review this document for completeness
2. Update STRUCTURE.md with selected patterns
3. Create code templates/scaffolding with these patterns baked in
