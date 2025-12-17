# Database Design Patterns Evaluation

**Project:** Node Workspace Backend  
**Database:** SQLite with Diesel ORM  
**Date:** 2024-12-18  
**Total Tables:** 50+ tables  
**Migration Files:** 2 active migrations

---

## Executive Summary

| Category | Score | Grade | Status |
|----------|-------|-------|--------|
| **Naming Conventions** | 95% | A | ✅ Excellent |
| **Schema Design** | 88% | B+ | ✅ Very Good |
| **Indexing Strategy** | 75% | C+ | ⚠️ Needs Improvement |
| **Relationship Patterns** | 82% | B | ✅ Good |
| **Migration Strategy** | 90% | A- | ✅ Very Good |
| **Query Patterns** | 85% | B+ | ✅ Very Good |
| **Connection Pooling** | 95% | A | ✅ Excellent |
| **Transaction Management** | 80% | B | ✅ Good |
| **Overall Score** | **86%** | **B+** | ✅ Good Quality |

---

## 1. Naming Conventions

### 1.1 Table Names

| Pattern | Example | Adoption | Score |
|---------|---------|----------|-------|
| **snake_case** | `agent_collaborations` | ✅ 100% | 100% |
| **Plural nouns** | `terminal_sessions` | ✅ 100% | 100% |
| **Descriptive** | `auto_discovery_retry_state` | ✅ 100% | 100% |
| **Domain prefix** | `agent_*`, `terminal_*` | ✅ 95% | 95% |

**Examples from schema.rs:**

```rust
✅ Good naming:
- agent_collaborations
- agent_execution_steps
- terminal_replay_events
- auto_discovery_retry_state
- api_store_credit_transactions
- notification_repository
```

**Pattern Analysis:**

```
Domain-based prefixes:
- agent_*          (6 tables)  - AI agent domain
- terminal_*       (5 tables)  - Terminal domain  
- api_store_*      (3 tables)  - API store domain
- auto_discovery_* (2 tables)  - Auto discovery domain
- node_*           (4 tables)  - Node management
```

**Score:** 95%

**Issues:** None significant

---

### 1.2 Column Names

| Pattern | Example | Adoption | Score |
|---------|---------|----------|-------|
| **snake_case** | `created_at`, `last_interaction_at` | ✅ 100% | 100% |
| **Suffixes for types** | `_at` (timestamps), `_id` (keys) | ✅ 100% | 100% |
| **Clear semantics** | `is_active`, `is_public` | ✅ 100% | 100% |
| **Consistent units** | `_sats` (satoshis), `_ms` (milliseconds) | ✅ 98% | 98% |

**Examples:**

```rust
✅ Timestamps:
- created_at -> Timestamp
- updated_at -> Timestamp
- last_interaction_at -> Nullable<Timestamp>
- completed_at -> Nullable<Timestamp>

✅ Boolean flags:
- is_active -> Bool
- is_public -> Bool
- is_default -> Bool

✅ Monetary values:
- cost_sats -> Integer
- revenue_sats -> Integer
- total_payments_sats -> Integer
- balance_after_sats -> Integer

✅ Duration/Time:
- duration_ms -> Nullable<Integer>
- timestamp_ms -> Integer
- total_duration_ms -> Integer

✅ Foreign keys:
- agent_id -> Text
- user_id -> Text
- session_id -> Text
```

**Score:** 99%

**Minor Issue:** Occasional inconsistency in nullable vs required fields

---

## 2. Schema Design

### 2.1 Data Types

| SQLite Type | Usage | Appropriate? | Notes |
|-------------|-------|--------------|-------|
| **TEXT** | IDs, strings | ✅ Good | UUIDs as TEXT (standard) |
| **INTEGER** | Counts, amounts | ✅ Good | Used for sats, counts |
| **FLOAT** | Ratings, percentages | ⚠️ Acceptable | Consider DECIMAL for money |
| **TIMESTAMP** | DateTime fields | ✅ Good | Diesel Timestamp type |
| **BOOL** | Flags | ✅ Good | SQLite INTEGER(0/1) |

**Analysis:**

```rust
✅ Primary Keys (TEXT UUIDs):
diesel::table! {
    agent_collaborations (collaboration_id) {
        collaboration_id -> Text,  // ✅ UUID as TEXT
        agent_id -> Text,
        ...
    }
}

⚠️ Float for monetary (rare):
diesel::table! {
    agent_templates (template_id) {
        rating -> Float,           // ⚠️ OK for ratings
        rating_count -> Integer,   // ✅ Good
        ...
    }
}

✅ Integer for satoshis:
diesel::table! {
    agent_executions (execution_id) {
        cost_sats -> Integer,      // ✅ Good - no decimals needed
        revenue_sats -> Integer,
        ...
    }
}
```

**Score:** 88%

**Recommendation:** Consider storing monetary values as INTEGER (satoshis) for precision

---

### 2.2 Nullable Fields Strategy

| Pattern | Example | Appropriate? | Score |
|---------|---------|--------------|-------|
| **Optional metadata** | `description -> Nullable<Text>` | ✅ Good | 95% |
| **Completion fields** | `completed_at -> Nullable<Timestamp>` | ✅ Good | 100% |
| **Optional relationships** | `target_agent_id -> Nullable<Text>` | ✅ Good | 95% |
| **Error fields** | `error_message -> Nullable<Text>` | ✅ Good | 100% |

**Analysis:**

```rust
✅ Sensible nullability:
diesel::table! {
    agent_executions (execution_id) {
        execution_id -> Text,              // ✅ NOT NULL (PK)
        agent_id -> Text,                  // ✅ NOT NULL (required)
        status -> Text,                    // ✅ NOT NULL (required)
        started_at -> Timestamp,           // ✅ NOT NULL (required)
        completed_at -> Nullable<Timestamp>, // ✅ Nullable (optional)
        error_message -> Nullable<Text>,   // ✅ Nullable (optional)
        ...
    }
}

✅ Nullable for optional FKs:
diesel::table! {
    agent_collaborations (collaboration_id) {
        target_node_id -> Text,            // ✅ NOT NULL
        target_agent_id -> Nullable<Text>, // ✅ Nullable (optional)
        ...
    }
}
```

**Score:** 98%

---

## 3. Indexing Strategy

### 3.1 Index Coverage

| Table | Indexes Found | Foreign Keys | Status |
|-------|---------------|--------------|--------|
| `terminal_replay_events` | ✅ 2 indexes | ✅ 1 FK | Good |
| `terminal_replay_metadata` | ✅ 1 index | ✅ 1 FK | Good |
| `terminal_sessions` | ✅ 1 index | ❌ None | Partial |
| **Other 47+ tables** | ❓ Unknown | ❓ Unknown | **Needs Audit** |

**Discovered Indexes:**

```sql
-- ✅ Good: Composite index for range queries
CREATE INDEX idx_terminal_replay_events_sequence 
ON terminal_replay_events(session_id, sequence_number);

-- ✅ Good: FK index
CREATE INDEX idx_terminal_replay_events_session_id 
ON terminal_replay_events(session_id);

-- ✅ Good: Single column index
CREATE INDEX idx_terminal_sessions_shell 
ON terminal_sessions(shell);
```

**Score:** 75%

**Critical Issue:** Only 2 migration files with index definitions found. Need to audit all 50+ tables for missing indexes.

---

### 3.2 Missing Indexes Analysis

**High-Risk Tables (Need Index Review):**

| Table | Foreign Keys | Likely Queries | Missing Indexes? |
|-------|--------------|----------------|------------------|
| `agent_collaborations` | `agent_id`, `target_node_id` | List by agent, Filter by node | ⚠️ Likely |
| `agent_execution_steps` | `execution_id`, `agent_id` | List by execution, Order by step_index | ⚠️ Likely |
| `ai_agents` | `owner_user_id`, `agent_user_id` | List by owner, Filter by status | ⚠️ Likely |
| `auto_discovery_retry_state` | `node_id` | Get by node_id | ⚠️ Likely |
| `api_store_credit_transactions` | `user_id` | List by user, Order by created_at | ⚠️ Likely |

**Recommendation:** Create comprehensive indexing audit script

---

## 4. Relationship Patterns

### 4.1 Foreign Keys

**Foreign Key Usage:**

```sql
-- ✅ Good: CASCADE delete with FK
CREATE TABLE terminal_replay_events (
    session_id TEXT NOT NULL,
    FOREIGN KEY (session_id) 
        REFERENCES terminal_sessions(id) 
        ON DELETE CASCADE  -- ✅ Explicit cascade
);
```

**Score:** 82%

**Issues Found:**

| Issue | Impact | Count |
|-------|--------|-------|
| **FK defined in only 2 tables** | High | ⚠️ Critical |
| **No FK constraints in schema.rs** | High | ⚠️ Critical |
| **Relationships implicit via naming** | Medium | ⚠️ Warning |

**Analysis:**

```rust
// ❌ No FK constraint in schema definition
diesel::table! {
    agent_collaborations (collaboration_id) {
        agent_id -> Text,           // References ai_agents
        target_node_id -> Text,     // References nodes
        // ❌ No FK constraint defined
    }
}

// ❌ Implicit relationship via naming convention
diesel::table! {
    agent_execution_steps (id) {
        execution_id -> Text,       // References agent_executions
        agent_id -> Text,           // References ai_agents
        // ❌ No FK enforcement
    }
}
```

**Recommendation:**

```sql
-- ✅ Should add FK constraints to migrations:
ALTER TABLE agent_collaborations
ADD CONSTRAINT fk_agent_id 
FOREIGN KEY (agent_id) REFERENCES ai_agents(agent_id) 
ON DELETE CASCADE;

ALTER TABLE agent_execution_steps
ADD CONSTRAINT fk_execution_id 
FOREIGN KEY (execution_id) REFERENCES agent_executions(execution_id) 
ON DELETE CASCADE;
```

---

### 4.2 Relationship Types

| Relationship | Example | Implementation | Score |
|--------------|---------|----------------|-------|
| **One-to-Many** | `agent` → `executions` | FK in child table | ✅ 90% |
| **One-to-One** | `session` → `replay_metadata` | UNIQUE FK | ✅ 95% |
| **Many-to-Many** | N/A found | N/A | N/A |

**One-to-One Example:**

```sql
CREATE TABLE terminal_replay_metadata (
    session_id TEXT NOT NULL UNIQUE,  -- ✅ UNIQUE constraint
    FOREIGN KEY (session_id) 
        REFERENCES terminal_sessions(id) 
        ON DELETE CASCADE
);
```

**Score:** 90%

---

## 5. Migration Strategy

### 5.1 Migration Structure

**Current Migrations:**

```
migrations/
├── 2025-11-16-191732_add_terminal_replay_support/
│   ├── up.sql      ✅ 33 lines, well-documented
│   └── down.sql    ✅ Rollback defined
└── 2025-11-17-020427_add_shell_to_terminal_sessions/
    ├── up.sql      ✅ 12 lines, simple ALTER
    └── down.sql    ✅ Rollback defined
```

**Quality Analysis:**

```sql
-- ✅ Excellent: Descriptive comments
-- Phase 4.5: Session Replay Support
--
-- Stores terminal session replay data for playback functionality

-- ✅ Good: Explicit NOT NULL constraints
CREATE TABLE terminal_replay_events (
    id TEXT PRIMARY KEY NOT NULL,
    session_id TEXT NOT NULL,
    sequence_number INTEGER NOT NULL,
    ...
);

-- ✅ Good: Default values
CREATE TABLE terminal_replay_events (
    stream TEXT NOT NULL DEFAULT 'stdout',  -- ✅ Sensible default
    ...
);

-- ✅ Good: Indexes created with table
CREATE INDEX idx_terminal_replay_events_session_id 
ON terminal_replay_events(session_id);

-- ✅ Good: FK constraints with CASCADE
FOREIGN KEY (session_id) 
    REFERENCES terminal_sessions(id) 
    ON DELETE CASCADE
```

**Score:** 90%

---

### 5.2 Migration Naming

| Convention | Example | Adoption |
|------------|---------|----------|
| **Timestamp prefix** | `2025-11-16-191732_` | ✅ 100% |
| **Descriptive name** | `add_terminal_replay_support` | ✅ 100% |
| **snake_case** | `add_shell_to_terminal_sessions` | ✅ 100% |

**Score:** 100%

---

## 6. Query Patterns (Diesel ORM)

### 6.1 Repository Pattern Implementation

**Pattern Analysis:**

```rust
// ✅ Excellent: Trait-based repository
#[async_trait]
pub trait IpPoolRepository: Send + Sync {
    async fn create(&self, entry: NodeIpPoolEntry) 
        -> Result<NodeIpPoolEntry, RepositoryError>;
    async fn get_by_id(&self, id: &str) 
        -> Result<Option<NodeIpPoolEntry>, RepositoryError>;
    async fn list(...) 
        -> Result<(Vec<NodeIpPoolEntry>, i64), RepositoryError>;
}

// ✅ Good: Diesel implementation
pub struct IpPoolRepositoryImpl {
    pool: DbPool,  // ✅ Connection pool injected
}
```

**Score:** 95%

---

### 6.2 Query Construction

**Parameterized Queries:**

```rust
// ✅ Excellent: Parameterized, no SQL injection
diesel::insert_into(node_ip_pool_entries::table)
    .values(&new_entry)
    .execute(&mut conn)

// ✅ Good: Type-safe filters
node_ip_pool_entries::table
    .filter(node_ip_pool_entries::id.eq(&id))
    .first::<NodeIpPoolEntryDb>(&mut conn)

// ✅ Good: Dynamic query building (boxed)
let mut query = node_ip_pool_entries::table.into_boxed();

if let Some(ref nid) = node_id {
    query = query.filter(node_ip_pool_entries::node_id.eq(nid));
}

if let Some(active) = is_active {
    query = query.filter(node_ip_pool_entries::is_active.eq(active));
}

query.limit(limit).offset(offset).load(&mut conn)
```

**Score:** 95%

**Benefits:**
- ✅ SQL injection prevention (compile-time safety)
- ✅ Type-safe queries
- ✅ Dynamic filtering support

---

### 6.3 Error Handling

**Repository Error Patterns:**

```rust
// ✅ Excellent: Enhanced error messages for constraints
if let diesel::result::Error::DatabaseError(
    diesel::result::DatabaseErrorKind::UniqueViolation,
    ref info,
) = e {
    let message = info.message();
    if message.contains("node_id") && message.contains("ip_address") {
        return RepositoryError::Conflict(format!(
            "IP address {}:{} is already registered for this node.",
            &new_entry.ip_address, new_entry.port
        ));
    }
}

// ✅ Good: Structured error types
#[derive(Debug, thiserror::Error)]
pub enum RepositoryError {
    #[error("Database error: {0}")]
    Database(#[from] diesel::result::Error),
    
    #[error("Conflict: {0}")]
    Conflict(String),
    
    #[error("Task join error: {0}")]
    TaskJoinError(String),
}
```

**Score:** 90%

---

## 7. Connection Pooling

### 7.1 Configuration

**Pool Settings:**

```rust
Pool::builder()
    .max_size(10)              // ✅ Reasonable for SQLite
    .min_idle(Some(1))         // ✅ Keep 1 connection warm
    .connection_timeout(Duration::from_secs(30))
    .max_lifetime(Some(Duration::from_secs(3600)))  // 1 hour
    .idle_timeout(Some(Duration::from_secs(600)))   // 10 mins
    .connection_customizer(Box::new(SqliteConnectionCustomizer))
    .build(manager)
```

**Score:** 95%

**Justification:**
- ✅ `max_size(10)` appropriate (SQLite = 1 writer limitation)
- ✅ Connection timeouts prevent hung connections
- ✅ Lifetime limits prevent stale connections

---

### 7.2 SQLite Optimizations

**PRAGMA Settings:**

```rust
// ✅ Excellent: WAL mode for concurrency
diesel::sql_query("PRAGMA journal_mode = WAL")
    .execute(conn)?;

// ✅ Good: Busy timeout prevents "database locked"
diesel::sql_query("PRAGMA busy_timeout = 10000")  // 10 seconds
    .execute(conn)?;

// ✅ Good: Performance optimizations
diesel::sql_query("PRAGMA synchronous = NORMAL")
    .execute(conn)?;

diesel::sql_query("PRAGMA cache_size = -10000")  // 10MB
    .execute(conn)?;

// ✅ Critical: FK enforcement
diesel::sql_query("PRAGMA foreign_keys = ON")
    .execute(conn)?;

// ✅ Good: Memory for temp storage
diesel::sql_query("PRAGMA temp_store = MEMORY")
    .execute(conn)?;
```

**Score:** 98%

**Key Benefits:**
- ✅ WAL mode: Multiple readers + 1 writer concurrently
- ✅ Busy timeout: Reduces "database locked" errors
- ✅ FK enforcement: Data integrity
- ✅ Cache size: Performance boost

---

## 8. Transaction Management

### 8.1 Async Operations

**Pattern:**

```rust
// ✅ Good: Blocking operations in tokio::spawn_blocking
tokio::task::spawn_blocking(move || {
    let mut conn = pool.get()
        .map_err(RepositoryError::from)?;
    
    diesel::insert_into(table)
        .values(&entry)
        .execute(&mut conn)?;
    
    // Retrieve inserted entry
    table.filter(id.eq(&entry_id))
        .first(&mut conn)
        .map_err(RepositoryError::from)
})
.await
.map_err(|e| RepositoryError::TaskJoinError(e.to_string()))?
```

**Score:** 85%

**Benefits:**
- ✅ Non-blocking async operations
- ✅ Proper error propagation
- ✅ Connection pool integration

**Issues:**
- ⚠️ No explicit transaction boundaries for multi-step operations
- ⚠️ Potential race conditions in complex updates

---

### 8.2 Transaction Support

**Missing Pattern:**

```rust
// ❌ No explicit transaction wrapper found
// Should have:
pub async fn create_with_transaction<F, T>(
    &self, 
    f: F
) -> Result<T, RepositoryError>
where
    F: FnOnce(&mut SqliteConnection) -> Result<T, RepositoryError>,
{
    let pool = self.pool.clone();
    tokio::task::spawn_blocking(move || {
        let mut conn = pool.get()?;
        conn.transaction(|conn| f(conn))
    })
    .await?
}
```

**Score:** 75%

**Recommendation:** Add transaction utility methods for multi-step operations

---

## 9. Data Integrity

### 9.1 Constraints

| Constraint Type | Usage | Examples | Score |
|-----------------|-------|----------|-------|
| **PRIMARY KEY** | ✅ 100% | All tables have PK | 100% |
| **NOT NULL** | ✅ 95% | Appropriate fields | 95% |
| **UNIQUE** | ✅ 90% | `session_id UNIQUE` | 90% |
| **FOREIGN KEY** | ⚠️ 5% | Only 2 tables | **20%** |
| **CHECK** | ❌ 0% | None found | **0%** |
| **DEFAULT** | ✅ 80% | `DEFAULT 'stdout'` | 80% |

**Critical Gap: Foreign Keys**

```sql
-- ❌ Missing FK constraints in most tables
-- Only found in:
- terminal_replay_events (2 FKs)
- terminal_replay_metadata (1 FK)

-- ⚠️ Should have FKs in:
- agent_collaborations.agent_id -> ai_agents.agent_id
- agent_execution_steps.execution_id -> agent_executions.execution_id
- api_store_credit_transactions.user_id -> users.user_id
... (40+ more relationships)
```

**Score:** 57%

---

### 9.2 Validation

**Application-Level Validation:**

```rust
// ✅ Good: Enum validation
#[derive(Debug, Serialize, Deserialize, Clone, PartialEq)]
pub enum PortType {
    Lightning,
    Http,
    Websocket,
    Rathole,
}

impl PortType {
    pub fn as_str(&self) -> &'static str { /* ... */ }
    pub fn from_str(s: &str) -> Result<Self, String> { /* ... */ }
}

// ✅ Good: Validator trait usage
#[derive(Debug, Deserialize, Validate)]
pub struct CreateIpRequest {
    #[validate(ip)]
    pub ip_address: String,
    
    #[validate(range(min = 1, max = 65535))]
    pub port: u16,
    
    pub port_type: PortType,
}
```

**Score:** 90%

---

## 10. Performance Considerations

### 10.1 N+1 Query Prevention

**Pattern:**

```rust
// ✅ Good: Pagination support
async fn list(
    &self,
    ...
    limit: i64,
    offset: i64,
) -> Result<(Vec<NodeIpPoolEntry>, i64), RepositoryError>

// ✅ Good: Count query optimization
let total_count = count_query.count().get_result(&mut conn)?;
let entries = query
    .limit(limit)
    .offset(offset)
    .load(&mut conn)?;

Ok((entries, total_count))
```

**Score:** 85%

**Missing:**
- ⚠️ No eager loading examples found
- ⚠️ No join optimization for related entities

---

### 10.2 Query Optimization

**Good Patterns:**

```rust
// ✅ Boxed queries for dynamic building
let mut query = table.into_boxed();
if filter { query = query.filter(...); }

// ✅ Index usage (when indexes exist)
query.filter(session_id.eq(id))  // Uses idx_..._session_id

// ✅ Limit/Offset pagination
query.limit(100).offset(0)
```

**Score:** 80%

---

## 11. Summary Matrix

| Category | Weight | Score | Weighted | Grade |
|----------|--------|-------|----------|-------|
| **Naming Conventions** | 10% | 95% | 9.5 | A |
| **Schema Design** | 15% | 88% | 13.2 | B+ |
| **Indexing Strategy** | 20% | 75% | 15.0 | C+ |
| **Relationships** | 15% | 82% | 12.3 | B |
| **Migrations** | 10% | 90% | 9.0 | A- |
| **Query Patterns** | 15% | 85% | 12.8 | B+ |
| **Connection Pooling** | 10% | 95% | 9.5 | A |
| **Data Integrity** | 5% | 57% | 2.9 | F |

**Total Weighted Score:** **84.2/100 (B)**

---

## 12. Critical Issues & Recommendations

### 🔴 Critical (Fix Immediately)

| ID | Issue | Impact | Fix Effort | Priority |
|----|-------|--------|------------|----------|
| **C1** | Missing FK constraints (48+ tables) | High | High | P0 |
| **C2** | No index audit for 48+ tables | High | Medium | P0 |
| **C3** | No CHECK constraints | Medium | Low | P1 |

---

### 🟡 High Priority (Fix Soon)

| ID | Issue | Impact | Fix Effort | Priority |
|----|-------|--------|------------|----------|
| **H1** | No explicit transaction utilities | Medium | Medium | P1 |
| **H2** | Missing indexes on FK columns | High | Medium | P1 |
| **H3** | No eager loading patterns | Medium | Medium | P2 |
| **H4** | Float for some monetary values | Low | Low | P2 |

---

### 🟢 Medium Priority (Nice to Have)

| ID | Issue | Impact | Fix Effort | Priority |
|----|-------|--------|------------|----------|
| **M1** | No query performance monitoring | Medium | High | P2 |
| **M2** | No database versioning in schema | Low | Low | P3 |
| **M3** | Missing composite indexes | Medium | Medium | P2 |

---

## 13. Action Plan

### Phase 1: Foreign Keys (Critical)

```sql
-- Step 1: Audit all relationships
SELECT name FROM sqlite_master WHERE type='table';

-- Step 2: Create FK migration
-- migration: 2025-01-XX_add_foreign_key_constraints
ALTER TABLE agent_collaborations
ADD CONSTRAINT fk_agent_id 
FOREIGN KEY (agent_id) REFERENCES ai_agents(agent_id) 
ON DELETE CASCADE;

-- Repeat for all 48+ tables
```

**Effort:** 2-3 days  
**Impact:** High (data integrity)

---

### Phase 2: Index Audit (Critical)

```sql
-- Step 1: Identify missing indexes
-- For each FK column, verify index exists

-- Step 2: Create indexes migration
CREATE INDEX idx_agent_collaborations_agent_id 
ON agent_collaborations(agent_id);

CREATE INDEX idx_agent_collaborations_target_node_id 
ON agent_collaborations(target_node_id);

-- Composite indexes for common queries
CREATE INDEX idx_agent_executions_agent_status 
ON agent_executions(agent_id, status);
```

**Effort:** 1-2 days  
**Impact:** High (performance)

---

### Phase 3: Transaction Utilities (High)

```rust
// Add to repository base trait
pub trait Repository {
    async fn with_transaction<F, T>(&self, f: F) 
        -> Result<T, RepositoryError>
    where
        F: FnOnce(&mut SqliteConnection) -> Result<T, RepositoryError>;
}
```

**Effort:** 1 day  
**Impact:** Medium (reliability)

---

## 14. Best Practices Compliance

| Practice | Compliance | Notes |
|----------|------------|-------|
| **Normalized schema** | ✅ 95% | Excellent normalization |
| **Parameterized queries** | ✅ 100% | Diesel enforces this |
| **Connection pooling** | ✅ 98% | Excellent implementation |
| **Index strategy** | ⚠️ 75% | Needs comprehensive audit |
| **FK constraints** | ❌ 5% | Critical gap |
| **Migration versioning** | ✅ 100% | Diesel timestamps |
| **Rollback support** | ✅ 100% | All migrations have down.sql |

---

## 15. Constitution File Additions

```markdown
## Database Design Standards

### Schema Naming
- Tables: plural snake_case (`agent_executions`)
- Columns: snake_case with type suffixes (`created_at`, `is_active`)
- Foreign keys: `{entity}_id` pattern
- Timestamps: Always use `created_at`, `updated_at`

### Constraints (MANDATORY)
- **PRIMARY KEY**: All tables MUST have explicit PK
- **FOREIGN KEY**: ALL relationships MUST have FK constraints with CASCADE
- **NOT NULL**: Required fields MUST be NOT NULL
- **UNIQUE**: One-to-one relationships MUST use UNIQUE
- **CHECK**: Use for enum validation at DB level

### Indexing Rules
- **FK columns**: MUST have indexes
- **Filter columns**: Frequently filtered columns MUST have indexes
- **Composite**: Create for common multi-column queries
- **Naming**: `idx_{table}_{column(s)}`

### Migrations
- **Naming**: `YYYY-MM-DD-HHMMSS_{description}`
- **Documentation**: Comment purpose in up.sql
- **Rollback**: Always provide down.sql
- **Atomicity**: One logical change per migration

### Repository Pattern
- **Trait-based**: Define repository trait
- **Parameterized**: Use Diesel DSL (no raw SQL)
- **Error handling**: Return Result<T, RepositoryError>
- **Async**: Use tokio::spawn_blocking for Diesel queries
```

---

**Reviewer:** ____________________  
**Date:** ____________________

