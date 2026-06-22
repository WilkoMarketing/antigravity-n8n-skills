---
name: n8n-validation-expert
description: Interpret validation errors and guide fixing them. Use when encountering validation errors, validation warnings, false positives, operator structure issues, or need help understanding validation results. Also use when asking about validation profiles, error types, the validation loop process, or auto-fix capabilities. Consult this skill whenever a validate_node or validate_workflow call returns errors or warnings — it knows which warnings are false positives and which errors need real fixes.
---

# n8n Validation Expert

Expert guide for interpreting and fixing n8n validation errors.

---

## Validation Philosophy

**Validate early, validate often**

Validation is typically iterative:
- Expect validation feedback loops
- Usually 2-3 validate → fix cycles
- Average: 23s thinking about errors, 58s fixing them

**Key insight**: Validation is an iterative process, not one-shot!

---

## Error Severity Levels

### 1. Errors (Must Fix)
**Blocks workflow execution** - Must be resolved before activation

**Types**:
- `missing_required` - Required field not provided
- `invalid_value` - Value doesn't match allowed options
- `type_mismatch` - Wrong data type (string instead of number)
- `invalid_reference` - Referenced node doesn't exist
- `invalid_expression` - Expression syntax error

**Example**:
```json
{
  "type": "missing_required",
  "property": "channel",
  "message": "Channel name is required",
  "fix": "Provide a channel name (lowercase, no spaces, 1-80 characters)"
}
```

### 2. Warnings (Should Fix)
**Doesn't block execution** - Workflow can be activated but may have issues

**Types**:
- `best_practice` - Recommended but not required
- `deprecated` - Using old API/feature
- `performance` - Potential performance issue

**Example**:
```json
{
  "type": "best_practice",
  "property": "errorHandling",
  "message": "Slack API can have rate limits",
  "suggestion": "Add onError: 'continueRegularOutput' with retryOnFail"
}
```

### 3. Suggestions (Optional)
**Nice to have** - Improvements that could enhance workflow

**Types**:
- `optimization` - Could be more efficient
- `alternative` - Better way to achieve same result

---

## The Validation Loop

### Pattern from Telemetry
**7,841 occurrences** of this pattern:

```
1. Configure node
   ↓
2. validate_node (23 seconds thinking about errors)
   ↓
3. Read error messages carefully
   ↓
4. Fix errors
   ↓
5. validate_node again (58 seconds fixing)
   ↓
6. Repeat until valid (usually 2-3 iterations)
```

### Example
```javascript
// Iteration 1
let config = {
  resource: "channel",
  operation: "create"
};

const result1 = validate_node({
  nodeType: "nodes-base.slack",
  config,
  profile: "runtime"
});
// → Error: Missing "name"

// ⏱️  23 seconds thinking...

// Iteration 2
config.name = "general";

const result2 = validate_node({
  nodeType: "nodes-base.slack",
  config,
  profile: "runtime"
});
// → Error: Missing "text"

// ⏱️  58 seconds fixing...

// Iteration 3
config.text = "Hello!";

const result3 = validate_node({
  nodeType: "nodes-base.slack",
  config,
  profile: "runtime"
});
// → Valid! ✅
```

**This is normal!** Don't be discouraged by multiple iterations.

---

## Validation Profiles

Choose the right profile for your stage:

### minimal
**Use when**: Quick checks during editing

**Validates**:
- Only required fields
- Basic structure

**Pros**: Fastest, most permissive
**Cons**: May miss issues

### runtime (RECOMMENDED)
**Use when**: Pre-deployment validation

**Validates**:
- Required fields
- Value types
- Allowed values
- Basic dependencies

**Pros**: Balanced, catches real errors
**Cons**: Some edge cases missed

**This is the recommended profile for most use cases**

### ai-friendly
**Use when**: AI-generated configurations

**Validates**:
- Same as runtime
- Reduces false positives
- More tolerant of minor issues

**Pros**: Less noisy for AI workflows
**Cons**: May allow some questionable configs

### strict
**Use when**: Production deployment, critical workflows

**Validates**:
- Everything
- Best practices
- Performance concerns
- Security issues

**Pros**: Maximum safety
**Cons**: Many warnings, some false positives

---

## Common Error Types

Five core error types, in rough order of frequency:

- **`missing_required`** — a required field isn't provided. Use `get_node` to see required fields, then add it.
- **`invalid_value`** — value doesn't match allowed options (enums are case-sensitive). Check the error's allowed list or `get_node`.
- **`type_mismatch`** — wrong data type (string `"100"` vs number `100`). Convert to the expected type.
- **`invalid_expression`** — expression syntax error (missing `{{}}`, typos). See the n8n Expression Syntax skill.
- **`invalid_reference`** — referenced node doesn't exist (renamed, deleted, or misspelled). Fix the name or `cleanStaleConnections`.

A sixth class, **`patchNodeField` errors** (find-not-found, ambiguous match, invalid/unsafe regex), surfaces when a `patchNodeField` op fails during `n8n_update_partial_workflow` — it's strict by design and errors rather than silently continuing.

Every type above has worked examples (broken config → fix) plus the patchNodeField error cases and their fixes in **[ERROR_CATALOG.md](ERROR_CATALOG.md)**.

---

## Auto-Sanitization System

**Automatically fixes common operator structure issues** on ANY workflow update — `n8n_create_workflow`, `n8n_update_partial_workflow`, or any save. Trust it; don't hand-fix these.

**What it fixes**:
- **Binary operators** (equals, notEquals, contains, notContains, greaterThan, lessThan, startsWith, endsWith) — removes the wrong `singleValue` property.
- **Unary operators** (isEmpty, isNotEmpty, true, false) — adds `singleValue: true`.
- **IF/Switch metadata** — adds complete `conditions.options` metadata for IF v2.2+ and Switch v3.2+.

**What it CANNOT fix** (handle manually): broken connections to non-existent nodes (use `cleanStaleConnections`), branch-count mismatches (add/remove connections or rules), and paradoxical corrupt states (may need manual DB intervention).

Before/after examples and the full cannot-fix detail are in **[ERROR_CATALOG.md](ERROR_CATALOG.md)** (Auto-Sanitization sections).

---

## False Positives

Validation warnings that are technically "wrong" but acceptable in your use case. Not every warning needs a fix — many are context-dependent. Common ones and when each is acceptable vs. worth fixing:

- **"Missing error handling"** — OK for dev/testing and non-critical notifications; fix for production handling important data.
- **"No retry logic"** — OK for idempotent ops, APIs with their own retry, manual triggers; fix for flaky external services and production automation.
- **"Missing rate limiting"** — OK for internal/low-volume/server-side-limited APIs; fix for public, high-volume APIs.
- **"Unbounded query"** — OK for small known datasets, aggregations, dev/testing; fix for production queries on large tables.

**Reduce false positives** with the `ai-friendly` profile (e.g. `validate_node({nodeType, config, profile: "ai-friendly"})`).

Full per-case guidance, security/credential warnings, known n8n false-positive issues (#304, #306, #338), profile strategies, the "should I fix this?" decision framework, and how to document accepted warnings are in **[FALSE_POSITIVES.md](FALSE_POSITIVES.md)**.

---

## Validation Result Structure

### Complete Response
```javascript
{
  "valid": false,
  "errors": [
    {
      "type": "missing_required",
      "property": "channel",
      "message": "Channel name is required",
      "fix": "Provide a channel name (lowercase, no spaces)"
    }
  ],
  "warnings": [
    {
      "type": "best_practice",
      "property": "errorHandling",
      "message": "Slack API can have rate limits",
      "suggestion": "Add onError: 'continueRegularOutput'"
    }
  ],
  "suggestions": [
    {
      "type": "optimization",
      "message": "Consider using batch operations for multiple messages"
    }
  ],
  "summary": {
    "hasErrors": true,
    "errorCount": 1,
    "warningCount": 1,
    "suggestionCount": 1
  }
}
```

### How to Read It

1. **Check `valid` first** — `true` means the config is valid; `false` means there are errors to fix before deployment.
2. **Fix `errors` first** — each carries a `property`, `message`, and `fix`. These must be resolved.
3. **Review `warnings`** — each has a `message` and `suggestion`; decide per-case whether to address it (see False Positives above).
4. **Consider `suggestions`** — optional improvements, not required.

---

## Workflow Validation

### validate_workflow (Structure)
**Validates entire workflow**, not just individual nodes

**Checks**:
1. **Node configurations** - Each node valid
2. **Connections** - No broken references
3. **Expressions** - Syntax and references valid
4. **Flow** - Logical workflow structure

**Example**:
```javascript
validate_workflow({
  workflow: {
    nodes: [...],
    connections: {...}
  },
  options: {
    validateNodes: true,
    validateConnections: true,
    validateExpressions: true,
    profile: "runtime"
  }
})
```

### Common Workflow Errors

#### 1. Broken Connections
```json
{
  "error": "Connection from 'Transform' to 'NonExistent' - target node not found"
}
```

**Fix**: Remove stale connection or create missing node

#### 2. Circular Dependencies
```json
{
  "error": "Circular dependency detected: Node A → Node B → Node A"
}
```

**Fix**: Restructure workflow to remove loop

#### 3. Multiple Start Nodes
```json
{
  "warning": "Multiple trigger nodes found - only one will execute"
}
```

**Fix**: Remove extra triggers or split into separate workflows

#### 4. Disconnected Nodes
```json
{
  "warning": "Node 'Transform' is not connected to workflow flow"
}
```

**Fix**: Connect node or remove if unused

---

## Recovery Strategies

### Strategy 1: Start Fresh
**When**: Configuration is severely broken

**Steps**:
1. Note required fields from `get_node`
2. Create minimal valid configuration
3. Add features incrementally
4. Validate after each addition

### Strategy 2: Binary Search
**When**: Workflow validates but executes incorrectly

**Steps**:
1. Remove half the nodes
2. Validate and test
3. If works: problem is in removed nodes
4. If fails: problem is in remaining nodes
5. Repeat until problem isolated

### Strategy 3: Clean Stale Connections
**When**: "Node not found" errors

**Steps**:
```javascript
n8n_update_partial_workflow({
  id: "workflow-id",
  operations: [{
    type: "cleanStaleConnections"
  }]
})
```

### Strategy 4: Use Auto-fix
**When**: Validation errors that can be automatically resolved

**Steps**:
```javascript
// Preview fixes (default - doesn't apply)
n8n_autofix_workflow({
  id: "workflow-id",
  applyFixes: false,
  confidenceThreshold: "medium"  // high, medium, low
})

// Review fixes, then apply
n8n_autofix_workflow({
  id: "workflow-id",
  applyFixes: true
})
```

---

## Auto-Fix Capabilities

The `n8n_autofix_workflow` tool can fix these issue types:

1. **expression-format** - Missing `=` prefix in expressions (e.g., `{{ $json.field }}` → `={{ $json.field }}`)
2. **typeversion-correction** - Downgrades nodes with unsupported typeVersions
3. **error-output-config** - Removes conflicting onError settings
4. **node-type-correction** - Fixes unknown node types using similarity matching (90%+ confidence)
5. **webhook-missing-path** - Generates UUIDs for webhook nodes missing path configuration
6. **typeversion-upgrade** - Smart upgrades to latest node versions with auto-migration
7. **version-migration** - Guidance for complex breaking changes requiring manual steps

**Confidence levels**: `high` (90%+, safe to auto-apply), `medium` (70-89%, review recommended), `low` (<70%, manual review required)

```javascript
// Preview all fixes
n8n_autofix_workflow({id: "workflow-id"})

// Only apply high-confidence fixes
n8n_autofix_workflow({
  id: "workflow-id",
  applyFixes: true,
  confidenceThreshold: "high"
})

// Target specific fix types
n8n_autofix_workflow({
  id: "workflow-id",
  fixTypes: ["expression-format", "typeversion-upgrade"],
  applyFixes: true
})
```

**Post-update guidance**: For version upgrades, check the `postUpdateGuidance` field in the response for step-by-step migration instructions.

---

## Best Practices

### ✅ Do

- Validate after every significant change
- Read error messages completely
- Fix errors iteratively (one at a time)
- Use `runtime` profile for pre-deployment
- Check `valid` field before assuming success
- Trust auto-sanitization for operator issues
- Use `get_node` when unclear about requirements
- Document false positives you accept

### ❌ Don't

- Skip validation before activation
- Try to fix all errors at once
- Ignore error messages
- Use `strict` profile during development (too noisy)
- Assume validation passed (always check result)
- Manually fix auto-sanitization issues
- Deploy with unresolved errors
- Ignore all warnings (some are important!)

---

## Reviewing an existing workflow

Validating as you build (the loop above) is for catching schema and shape errors in your own in-progress work. **Reviewing an existing workflow** — yours or one you've been handed — is a different job: the workflow already passes `validate_workflow` clean, and you're hunting for the issues validation doesn't see (silent connection bugs, injection-prone queries, dropped-item Switches, Set/Code antipatterns, missing error paths). For that, pull the workflow with `n8n_get_workflow` and walk **[REVIEW_CHECKLIST.md](REVIEW_CHECKLIST.md)** — a severity-tiered audit (MUST FIX / SHOULD FIX / NICE TO HAVE) where every item points to the canonical skill for the fix. Run `n8n_audit_instance` alongside it to surface hardcoded secrets and unauthenticated webhooks across the whole instance.

---

## Detailed Guides

For comprehensive error catalogs, false positives, and workflow review:

- **[ERROR_CATALOG.md](ERROR_CATALOG.md)** - Complete list of error types with examples
- **[FALSE_POSITIVES.md](FALSE_POSITIVES.md)** - When warnings are acceptable
- **[REVIEW_CHECKLIST.md](REVIEW_CHECKLIST.md)** - Severity-tiered audit for reviewing an existing workflow

---

## Summary

**Key Points**:
1. **Validation is iterative** (avg 2-3 cycles, 23s + 58s)
2. **Errors must be fixed**, warnings are optional
3. **Auto-sanitization** fixes operator structures automatically
4. **Use runtime profile** for balanced validation
5. **False positives exist** - learn to recognize them
6. **Read error messages** - they contain fix guidance

**Validation Process**:
1. Validate → Read errors → Fix → Validate again
2. Repeat until valid (usually 2-3 iterations)
3. Review warnings and decide if acceptable
4. Deploy with confidence

**Related Skills & Tools**:
- n8n MCP Tools Expert - Use validation tools correctly
- n8n Expression Syntax - Fix expression errors
- n8n Node Configuration - Understand required fields
- `n8n_audit_instance` - Proactive security validation (hardcoded secrets, unauthenticated webhooks, missing error handling, data retention)
