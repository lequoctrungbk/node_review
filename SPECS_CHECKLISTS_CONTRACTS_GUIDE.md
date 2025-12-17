# Specs Checklists & Contracts Guide

**Purpose:** Comprehensive guide explaining the content and structure of `checklists/` and `contracts/` folders in feature specifications.

**Date:** 2024-12-18

---

## 📋 1. Checklists Folder (`checklists/`)

### Purpose
Quality gates and validation checklists để đảm bảo spec/implementation đạt chuẩn trước khi release.

### Typical Structure

```
checklists/
├── requirements.md          # ✅ Required (100%)
├── security.md              # ⚠️ Optional (22%)
├── architecture.md          # ⚠️ Optional (11%)
├── performance.md           # ⚠️ Optional (0%)
└── migration.md             # ⚠️ Optional (11%)
```

---

## 📝 1.1 Requirements Checklist (`requirements.md`)

### Format Standard

```markdown
# Specification Quality Checklist: [Feature Name]

**Purpose**: [What this checklist validates]
**Created**: YYYY-MM-DD
**Feature**: [Link to spec.md]
**Scope**: [Comprehensive/Release Gate/Quick Review]
**Focus Areas**: [List of focus areas]

## Category 1: [Category Name]

- [ ] CHK001 - [Question about requirement] [Type, Reference]
- [ ] CHK002 - [Question about requirement] [Type, Reference]
...

## Category 2: [Category Name]
...

## Validation Results

### [Category]: ✅ PASS / ⚠️ PARTIAL / ❌ FAIL
[Notes about validation]
```

### Nội dung điển hình:

#### A. **Specification Quality Checklist** (Pre-Planning Phase)

**Ví dụ:** `006-ip-pool-management/checklists/requirements.md`

| Section | Nội dung | Mục đích |
|---------|----------|----------|
| **Content Quality** | Kiểm tra spec không có implementation details | Đảm bảo spec technology-agnostic |
| **Requirement Completeness** | Kiểm tra requirements testable, measurable | Đảm bảo requirements đủ rõ ràng |
| **Feature Readiness** | Kiểm tra spec sẵn sàng cho planning phase | Release gate trước khi implement |

**Checklist items:**
```markdown
## Content Quality
- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders

## Requirement Completeness
- [x] No [NEEDS CLARIFICATION] markers
- [x] Requirements are testable and unambiguous
- [x] Success criteria are measurable
- [x] All acceptance scenarios defined
- [x] Edge cases identified

## Feature Readiness
- [x] All functional requirements have acceptance criteria
- [x] User scenarios cover primary flows
- [x] Feature meets Success Criteria
```

---

#### B. **Security & API Checklist** (Implementation Phase)

**Ví dụ:** `003-auto-friend-discovery/checklists/security-api.md`

| Category | Focus | Checklist Count |
|----------|-------|-----------------|
| **Authentication** | JWT validation, token handling | 7 items |
| **Rate Limiting** | Per-source/global limits, headers | 9 items |
| **Spam Protection** | Malicious nodes, Sybil attacks | 9 items |
| **L402 Integration** | Payment requirements, failures | 9 items |
| **Error Responses** | Error codes, sensitive data | 7 items |
| **Input Validation** | Format validation, sanitization | 8+ items |

**Pattern:**
```markdown
## Category: Authentication & Authorization

- [ ] CHK001 - Are auth requirements defined for /enable, /disable? [Completeness, Spec §FR-018]
- [ ] CHK002 - Is JWT validation specified with error codes (401/403)? [Clarity, Contract L186-273]
- [ ] CHK003 - Are diagnostic endpoints intentionally unauthenticated? [Gap, Contract L27-177]

## Category: Rate Limiting

- [ ] CHK008 - Is per-source limit (10 req/hour) quantified with HTTP 429? [Clarity, Spec §FR-017]
- [ ] CHK009 - Is global limit (100 req/min) specified? [Clarity, Spec §FR-011]
- [ ] CHK010 - Are rate limit headers (X-RateLimit-*) specified? [Gap, Contract]
```

**Validation status format:**
- `[ ]` - Not validated
- `[x]` - Validated & passing
- `[~]` - Partial validation

---

#### C. **Architecture Checklist** (Complex Features)

**Ví dụ:** `001-layered-ai-architecture/checklists/architecture.md`

| Category | Focus | Items |
|----------|-------|-------|
| **Trait Interface** | Method signatures, async/sync, bounds | 7 items |
| **Domain Organization** | Boundary criteria, dependencies | 6 items |
| **Type Safety** | DTO validation, error types | 6 items |
| **Protocol Abstraction** | Endpoint hiding, versioning | 5 items |
| **Metrics** | Calculation methods, persistence | 6+ items |

**Pattern:**
```markdown
## Core Trait Interface Requirements

- [ ] CHK001 - Are exact method signatures fully specified? [Completeness, Spec §FR-031]
- [ ] CHK002 - Are sync vs async requirements documented? [Clarity, Plan §Design]
- [ ] CHK003 - Are trait bounds (Send + Sync) justified? [Completeness, Gap]

## Test Validation Status

**E2E Test Suite**: `/path/to/test_file.rs`
**Test Results**: ✅ 42/46 tests passing (91.3% success rate)

### Failed Tests (Non-Blocking)
1. test_chk032 - Timing sensitivity (environment issue)
2. test_chk036 - Connection pool limitation
```

---

## 📄 1.2 Other Checklist Types

### Security Checklist (`security.md`)

```markdown
## Authentication Security
- [ ] Password hashing (bcrypt/Argon2)
- [ ] JWT secret rotation
- [ ] Token expiration handling

## API Security
- [ ] Input sanitization
- [ ] SQL injection prevention
- [ ] XSS protection

## Data Protection
- [ ] Sensitive data encryption at rest
- [ ] TLS 1.3 for data in transit
- [ ] PII handling compliance
```

### Performance Checklist (`performance.md`)

```markdown
## Response Time Requirements
- [ ] API endpoints < 200ms (p50)
- [ ] API endpoints < 500ms (p95)
- [ ] Database queries < 100ms

## Scalability
- [ ] Supports 1000 concurrent users
- [ ] Database connection pooling
- [ ] Caching strategy defined

## Resource Usage
- [ ] Memory usage < 512MB
- [ ] CPU usage < 70% under load
```

---

## 📑 2. Contracts Folder (`contracts/`)

### Purpose
Formal API contracts định nghĩa interface giữa services, endpoints, protocols.

### Typical Structure

```
contracts/
├── {feature}-api.yaml           # ✅ REST API (OpenAPI 3.0.3)
├── websocket-events.yaml        # ⚠️ WebSocket events (if needed)
├── grpc-service.proto           # ⚠️ gRPC (if needed)
└── event-schemas.yaml           # ⚠️ Event bus (if needed)
```

---

## 📝 2.1 REST API Contract (OpenAPI 3.0.3)

### Standard Structure

**Ví dụ:** `006-ip-pool-management/contracts/ip-pool-api.yaml`

```yaml
openapi: 3.0.3
info:
  title: [Feature] API
  description: |
    [Detailed description]
  version: 1.0.0
  contact:
    name: [Team Name]

servers:
  - url: http://localhost:3001/api/v2
    description: Local development

tags:
  - name: Internal [Feature]
    description: JWT authentication
  - name: External [Feature]
    description: L402 authentication

paths:
  /resource:
    get: ...
    post: ...
    
components:
  securitySchemes:
    jwt: ...
    l402: ...
    
  schemas:
    [DTOs, Enums, Errors]
    
  responses:
    [Standard error responses]
```

### Nội dung chi tiết:

#### A. **Info Section**

```yaml
info:
  title: IP Pool Management API
  description: |
    API endpoints for managing node IP addresses with health monitoring,
    priority-based selection, and automatic failover.
  version: 1.0.0
  contact:
    name: Economy V1 Team
```

#### B. **Security Schemes**

```yaml
components:
  securitySchemes:
    jwt:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: JWT token for internal API (Claims extractor)
      
    l402:
      type: apiKey
      in: header
      name: Authorization
      description: L402 Lightning payment auth (NodeId extractor)
```

#### C. **Tags Organization**

```yaml
tags:
  - name: Internal IP Pool
    description: Internal API endpoints (JWT authentication)
  - name: External IP Pool
    description: External API endpoints (L402 authentication)
  - name: Health Checks
    description: Health monitoring endpoints
```

**Rule:** Phân biệt Internal (JWT) vs External (L402)

#### D. **Path Definitions**

```yaml
paths:
  /ip-pool:
    get:
      tags: [Internal IP Pool]
      summary: List IP pool entries
      description: Retrieve all IPs with optional filtering
      operationId: listIpPool
      security:
        - jwt: []
      parameters:
        - name: port_type
          in: query
          required: false
          schema:
            $ref: '#/components/schemas/PortType'
      responses:
        '200':
          description: List of IP pool entries
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ApiResponseIpPoolList'
        '401':
          $ref: '#/components/responses/UnauthorizedError'
```

**Key elements:**
- ✅ operationId (unique identifier)
- ✅ security scheme reference
- ✅ Parameter validation (type, required, constraints)
- ✅ Response schemas với status codes
- ✅ Error responses

#### E. **Schema Definitions**

**Enums:**
```yaml
PortType:
  type: string
  enum:
    - lightning
    - http
    - websocket
    - rathole
  description: Service type requiring IP configuration
```

**Request DTOs:**
```yaml
CreateIpRequest:
  type: object
  required:
    - ip_address
    - port
    - port_type
  properties:
    ip_address:
      type: string
      description: IPv4 or IPv6 address
      example: "203.0.113.42"
    port:
      type: integer
      minimum: 1
      maximum: 65535
    port_type:
      $ref: '#/components/schemas/PortType'
```

**Response DTOs:**
```yaml
IpPoolResponse:
  type: object
  required:
    - id
    - node_id
    - ip_address
    - created_at
  properties:
    id:
      type: string
      format: uuid
    health_metrics:
      $ref: '#/components/schemas/HealthMetrics'
```

**API Response Wrapper:**
```yaml
ApiResponseIpPool:
  allOf:
    - $ref: '#/components/schemas/ApiResponseBase'
    - type: object
      properties:
        data:
          $ref: '#/components/schemas/IpPoolResponse'

ApiResponseBase:
  type: object
  required:
    - success
    - message
  properties:
    success:
      type: boolean
    message:
      type: string
    error:
      $ref: '#/components/schemas/ErrorDetail'
```

#### F. **Error Responses**

```yaml
components:
  responses:
    ValidationError:
      description: Request validation failed
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ApiError'
          example:
            success: false
            message: "Validation failed"
            error:
              code: "VALIDATION_ERROR"
              details:
                ip_address: "Invalid format"

ErrorDetail:
  type: object
  properties:
    code:
      type: string
      enum:
        - VALIDATION_ERROR
        - AUTHENTICATION_ERROR
        - NOT_FOUND
        - CONFLICT
        - INTERNAL_ERROR
```

---

## 📝 2.2 WebSocket Events Contract

**Ví dụ:** `006-ip-pool-management/contracts/websocket-events.yaml`

### Structure

```yaml
openapi: 3.0.3
info:
  title: [Feature] WebSocket Events
  description: |
    WebSocket event schemas for real-time updates
  version: 1.0.0

paths: {}  # No HTTP paths for WebSocket

components:
  schemas:
    WebSocketEventType:
      type: string
      enum:
        - ip_pool:created
        - ip_pool:updated
        - ip_pool:health_changed

    WebSocketMessage:
      type: object
      required:
        - type
        - timestamp
        - data
      properties:
        type:
          $ref: '#/components/schemas/WebSocketEventType'
        timestamp:
          type: string
          format: date-time
        data:
          oneOf:
            - $ref: '#/components/schemas/IpPoolCreatedData'
            - $ref: '#/components/schemas/IpPoolUpdatedData'
```

### Event Payload Schemas

```yaml
IpPoolCreatedData:
  type: object
  required:
    - ip_entry
  properties:
    ip_entry:
      $ref: '#/components/schemas/IpPoolEntry'
  description: Payload when new IP added
  example:
    ip_entry:
      id: "550e8400-..."
      ip_address: "203.0.113.42"
      port: 9735

IpPoolHealthChangedData:
  type: object
  required:
    - ip_entry_id
    - health_metrics
    - previous_state
  properties:
    ip_entry_id:
      type: string
      format: uuid
    health_metrics:
      $ref: '#/components/schemas/HealthMetrics'
    previous_state:
      $ref: '#/components/schemas/ConnectionState'
    was_auto_deactivated:
      type: boolean
```

### Example Messages

```yaml
examples:
  IpCreatedEvent:
    summary: IP Pool Created Event
    value:
      type: "ip_pool:created"
      timestamp: "2025-01-10T12:45:30Z"
      data:
        ip_entry:
          id: "550e8400-..."
          ip_address: "203.0.113.42"
```

---

## 📝 2.3 Architecture Contract Documents (.md)

**Ví dụ:** `001-layered-ai-architecture/contracts/`

Cho architectural specs, contracts có thể là markdown files:

```
contracts/
├── repository-traits.md         # Repository trait interfaces
├── service-traits.md            # Service trait interfaces
├── transport-manager.md         # Transport abstraction
├── protocol-adapter.md          # Protocol adapter patterns
└── metrics-persistence.md       # Metrics storage contracts
```

**Content:**
```markdown
# Repository Trait Contract

## Interface Definition

```rust
#[async_trait]
pub trait UserRepository: Send + Sync {
    async fn create(&self, req: CreateUserRequest) 
        -> Result<User, RepositoryError>;
    async fn get_by_id(&self, id: &str) 
        -> Result<Option<User>, RepositoryError>;
}
```

## Error Handling

Repository operations MUST return `Result<T, RepositoryError>`.

## Constraints

- ALL implementations MUST be `Send + Sync`
- NO business logic in repositories
- NO direct transport protocol usage
```

---

## 📊 3. Comparison Matrix

| Aspect | Checklists | Contracts |
|--------|-----------|-----------|
| **Format** | Markdown checklist | OpenAPI YAML / Markdown |
| **Purpose** | Quality validation | API interface definition |
| **Audience** | Reviewers, QA | Developers, Integrators |
| **Phase** | Pre/Post implementation | Design & Implementation |
| **Executable** | Manual checks / Tests | Code generation possible |
| **Version Control** | Track validation status | Track API changes |

---

## 🎯 4. Best Practices

### Checklists

✅ **DO:**
- Start with CHK001, CHK002, etc. (numbered)
- Link to spec sections: `[Spec §FR-018]`
- Include validation status: `[ ]`, `[x]`, `[~]`
- Group by category (10-15 items per category)
- Add validation results section
- Link to test files if applicable

❌ **DON'T:**
- Mix different validation phases in one file
- Create overly generic checklists
- Skip reference links
- Leave ambiguous items

### Contracts

✅ **DO:**
- Follow OpenAPI 3.0.3 strictly
- Use `$ref` for schema reuse
- Include examples for all schemas
- Document error codes comprehensively
- Separate Internal (JWT) vs External (L402)
- Version your APIs (v1, v2)

❌ **DON'T:**
- Mix REST and WebSocket in same file
- Use generic names (`openapi.yaml` → `feature-api.yaml`)
- Skip security scheme definitions
- Forget required/optional markers
- Omit validation constraints (min/max)

---

## 📋 5. Templates

### requirements.md Template

```markdown
# Specification Quality Checklist: [Feature Name]

**Purpose**: [Description]
**Created**: YYYY-MM-DD
**Feature**: [../spec.md](../spec.md)

## Content Quality

- [ ] CHK001 - No implementation details
- [ ] CHK002 - Focused on user value
- [ ] CHK003 - Written for non-technical stakeholders

## Requirement Completeness

- [ ] CHK010 - Requirements testable
- [ ] CHK011 - Success criteria measurable
- [ ] CHK012 - Edge cases identified

## Validation Results

### Content Quality: ⬜ Not Validated
### Requirement Completeness: ⬜ Not Validated
```

### api.yaml Template

```yaml
openapi: 3.0.3
info:
  title: [Feature] API
  version: 1.0.0

servers:
  - url: http://localhost:3001/api/v2

tags:
  - name: Internal [Resource]
  - name: External [Resource]

paths:
  /resource:
    get:
      tags: [Internal [Resource]]
      operationId: listResource
      security:
        - jwt: []
      responses:
        '200':
          description: Success

components:
  securitySchemes:
    jwt:
      type: http
      scheme: bearer
  schemas:
    ApiResponseBase:
      type: object
      required: [success, message]
```

---

## 📚 6. References

- OpenAPI 3.0.3: https://spec.openapis.org/oas/v3.0.3
- JSON Schema: https://json-schema.org/
- Best example: `specs/006-ip-pool-management/`

---

**Kết luận:**

- **Checklists** = Quality gates (50-100+ items)
- **Contracts** = API definitions (OpenAPI + WebSocket)
- Cả hai đều critical cho spec quality!

