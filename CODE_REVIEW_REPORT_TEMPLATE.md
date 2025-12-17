# Code Review Report

**Project:** [Project Name]  
**Reviewer:** [Reviewer Name]  
**Review Date:** [YYYY-MM-DD]  
**Version/Branch:** [version or branch name]  
**Status:** 🔴 Critical Issues | 🟡 Needs Improvement | 🟢 Approved

---

## Executive Summary

> [Brief 2-3 sentence overview of the review findings and overall assessment]

---

## 1. Stack Consistency Assessment

### 1.1 Software Standards & Conventions

| Area | Current State | Recommendation | Priority |
|------|---------------|----------------|----------|
| Code Style | ⬜ Consistent ⬜ Inconsistent | | ⬜ High ⬜ Medium ⬜ Low |
| Naming Conventions | ⬜ Consistent ⬜ Inconsistent | | ⬜ High ⬜ Medium ⬜ Low |
| Error Handling | ⬜ Consistent ⬜ Inconsistent | | ⬜ High ⬜ Medium ⬜ Low |
| Logging Patterns | ⬜ Consistent ⬜ Inconsistent | | ⬜ High ⬜ Medium ⬜ Low |
| Testing Patterns | ⬜ Consistent ⬜ Inconsistent | | ⬜ High ⬜ Medium ⬜ Low |

**Findings:**
- 
- 

**Proposed Standards for Constitution File:**
```
[Define standards here that should be added to the AI constitution file]
```

---

### 1.2 Spec Format

| Aspect | Current State | Issues Found | Recommendation |
|--------|---------------|--------------|----------------|
| TypeScript/Type Definitions | | | |
| Interface Definitions | | | |
| Schema Validation | | | |
| Documentation Comments | | | |

**Findings:**
- 
- 

**Proposed Spec Standards:**
```
[Define spec format standards for constitution file]
```

---

### 1.3 Database Design Patterns

| Pattern | Current Implementation | Assessment | Recommendation |
|---------|----------------------|------------|----------------|
| Naming (tables/columns) | | ⬜ Good ⬜ Needs Work | |
| Relationships | | ⬜ Good ⬜ Needs Work | |
| Indexing Strategy | | ⬜ Good ⬜ Needs Work | |
| Migration Patterns | | ⬜ Good ⬜ Needs Work | |
| Query Patterns | | ⬜ Good ⬜ Needs Work | |

**Findings:**
- 
- 

**Proposed Database Standards:**
```
[Define database patterns for constitution file]
```

---

### 1.4 API Structure & Naming

| Aspect | Current State | Issues | Recommendation |
|--------|---------------|--------|----------------|
| Route Naming | | | |
| HTTP Methods Usage | | | |
| Request/Response Format | | | |
| Error Response Structure | | | |
| Versioning Strategy | | | |
| Authentication Pattern | | | |

**Findings:**
- 
- 

**API Consistency Score:** ⬜ Excellent ⬜ Good ⬜ Fair ⬜ Poor

**Proposed API Standards:**
```
[Define API standards for constitution file]
```

---

### 1.5 Folder Hierarchy & Naming Rules

**Current Structure Analysis:**

```
[Paste current folder structure here]
```

| Area | Current State | Issues | Recommendation |
|------|---------------|--------|----------------|
| Root Organization | | | |
| Module/Feature Structure | | | |
| Shared/Common Code | | | |
| Config Files Location | | | |
| Test Files Location | | | |

**Findings:**
- 
- 

**Proposed Folder Structure:**
```
[Define ideal folder structure for constitution file]
```

---

## 2. Code Simplicity & Clarity Assessment

### 2.1 Complexity Analysis

| Metric | Finding | Target | Action Needed |
|--------|---------|--------|---------------|
| Average Function Length | | <30 lines | |
| Cyclomatic Complexity | | <10 | |
| Nesting Depth | | <4 levels | |
| File Size | | <300 lines | |

### 2.2 Readability Issues

| File/Component | Issue | Severity | Suggested Fix |
|----------------|-------|----------|---------------|
| | | ⬜ High ⬜ Medium ⬜ Low | |
| | | ⬜ High ⬜ Medium ⬜ Low | |
| | | ⬜ High ⬜ Medium ⬜ Low | |

### 2.3 Abstraction Quality

| Area | Assessment | Notes |
|------|------------|-------|
| Over-engineering | ⬜ None ⬜ Minor ⬜ Major | |
| Under-abstraction | ⬜ None ⬜ Minor ⬜ Major | |
| Code Duplication | ⬜ None ⬜ Minor ⬜ Major | |
| Dead Code | ⬜ None ⬜ Minor ⬜ Major | |

**Simplification Opportunities:**
1. 
2. 
3. 

---

## 3. Technical Debt Assessment

### 3.1 Refactoring Flags 🚩

| Location | Issue Description | Impact | Effort | Priority |
|----------|-------------------|--------|--------|----------|
| | | ⬜ High ⬜ Medium ⬜ Low | ⬜ S ⬜ M ⬜ L ⬜ XL | P1/P2/P3 |
| | | ⬜ High ⬜ Medium ⬜ Low | ⬜ S ⬜ M ⬜ L ⬜ XL | P1/P2/P3 |
| | | ⬜ High ⬜ Medium ⬜ Low | ⬜ S ⬜ M ⬜ L ⬜ XL | P1/P2/P3 |

### 3.2 Hidden Risks ⚠️

| Risk Category | Description | Likelihood | Impact | Mitigation |
|---------------|-------------|------------|--------|------------|
| Security | | ⬜ High ⬜ Medium ⬜ Low | ⬜ Critical ⬜ Major ⬜ Minor | |
| Performance | | ⬜ High ⬜ Medium ⬜ Low | ⬜ Critical ⬜ Major ⬜ Minor | |
| Scalability | | ⬜ High ⬜ Medium ⬜ Low | ⬜ Critical ⬜ Major ⬜ Minor | |
| Maintainability | | ⬜ High ⬜ Medium ⬜ Low | ⬜ Critical ⬜ Major ⬜ Minor | |
| Dependencies | | ⬜ High ⬜ Medium ⬜ Low | ⬜ Critical ⬜ Major ⬜ Minor | |

### 3.3 Debt Backlog (Prioritized)

| # | Item | Type | Business Impact | Recommended Timeline |
|---|------|------|-----------------|---------------------|
| 1 | | ⬜ Bug ⬜ Refactor ⬜ Security ⬜ Performance | | |
| 2 | | ⬜ Bug ⬜ Refactor ⬜ Security ⬜ Performance | | |
| 3 | | ⬜ Bug ⬜ Refactor ⬜ Security ⬜ Performance | | |

---

## 4. Additional Recommendations

### 4.1 Architecture Observations

> [Senior-level insights on overall architecture, patterns, and design decisions]

### 4.2 Best Practices Gaps

| Category | Current Gap | Industry Best Practice | Recommendation |
|----------|-------------|----------------------|----------------|
| Testing | | | |
| Documentation | | | |
| CI/CD | | | |
| Monitoring | | | |
| Security | | | |

### 4.3 Performance Considerations

- 
- 
- 

### 4.4 Scalability Concerns

- 
- 
- 

### 4.5 Team/Process Recommendations

- 
- 
- 

---

## 5. Constitution File Recommendations

> The following standards should be added to the AI constitution file for consistent code generation across the team:

### 5.1 Coding Standards

```markdown
# Coding Standards

## Naming Conventions
- [Define naming rules]

## File Organization
- [Define file organization rules]

## Code Style
- [Define style rules]
```

### 5.2 Architecture Standards

```markdown
# Architecture Standards

## Layer Separation
- [Define layer rules]

## Dependency Rules
- [Define dependency rules]

## Error Handling
- [Define error handling patterns]
```

### 5.3 API Standards

```markdown
# API Standards

## Endpoint Naming
- [Define endpoint naming rules]

## Request/Response Format
- [Define format rules]

## Error Codes
- [Define error code patterns]
```

### 5.4 Database Standards

```markdown
# Database Standards

## Table/Column Naming
- [Define naming rules]

## Relationship Patterns
- [Define relationship rules]

## Query Patterns
- [Define query patterns]
```

---

## 6. Action Items Summary

### Immediate (This Sprint)
- [ ] 
- [ ] 
- [ ] 

### Short-term (Next 2-4 Weeks)
- [ ] 
- [ ] 
- [ ] 

### Medium-term (1-3 Months)
- [ ] 
- [ ] 
- [ ] 

---

## Appendix

### A. Files Reviewed

| File Path | Lines | Review Status |
|-----------|-------|---------------|
| | | ⬜ Reviewed ⬜ Skimmed ⬜ Skipped |

### B. Tools Used

- Linter: 
- Static Analysis: 
- Security Scanner: 
- Other: 

### C. References

- [Link to relevant documentation]
- [Link to style guides]
- [Link to architecture decisions]

---

**Reviewer Signature:** ____________________  
**Date:** ____________________

**Review Approved By:** ____________________  
**Date:** ____________________

