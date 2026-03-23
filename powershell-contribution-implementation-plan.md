# PowerShell Contribution Implementation Plan

## Current Status

**What we had:** `fabric-powershell-cli` skill — well-audited, technically correct code examples (this branch has since been removed)

**Problem:** Doesn't align with repo architecture:
- "fabric" isn't an endpoint
- PowerShell is a language/access method, not an endpoint
- Covers multiple endpoints (workspaces, semantic models, lakehouses, capacities)
- Existing skills already cover these endpoints via `az rest`/`curl`

## Repo Architecture Rules

| Rule | Source |
|------|--------|
| Skills = single endpoint + single persona | architecture-overview.md |
| Agents = cross-endpoint orchestration | architecture-overview.md |
| Naming: `{endpoint}-{authoring|consumption}-{access}` | skill-authoring-guide.md |
| Common/ = shared patterns (not endpoint-specific) | common-folder-guide.md |
| "CLI" = interface pattern, not a language | skill-authoring-guide.md |

## Recommended Approach

### Option A: Add to common/ (Recommended)

Create `common/COMMON-POWERSHELL.md` with:
- Token acquisition patterns (`Get-AzAccessToken`, SecureString handling)
- REST API patterns (`Invoke-RestMethod`, pagination, LRO)
- CI/CD examples (Azure DevOps, GitHub Actions)
- Error handling and retry patterns

**Why:** PowerShell is an access method like `az rest`/`curl`. Existing skills (`powerbi-authoring-cli`, `sqldw-authoring-cli`) can reference it alongside `COMMON-CLI.md`.

**File structure:**
```
common/
├── COMMON-CORE.md
├── COMMON-CLI.md           # bash/az/curl patterns
├── COMMON-POWERSHELL.md    # NEW: PowerShell patterns
├── SQLDW-AUTHORING-CORE.md
└── ...
```

**Skill updates:**
- Add PowerShell section to `powerbi-authoring-cli` → references COMMON-POWERSHELL.md
- Add PowerShell section to `sqldw-authoring-cli` → references COMMON-POWERSHELL.md
- Add PowerShell section to `spark-authoring-cli` → references COMMON-POWERSHELL.md

### Option B: Create PowerShell-specific endpoint skills

Create `powerbi-authoring-powershell`, `lakehouse-authoring-powershell`, etc.

**Why not:** Duplicates content, violates DRY, multiple skills to maintain.

### Option C: Create an agent

Create `FabricPowerShellEngineer.agent.md` that orchestrates across endpoint skills.

**Why not:** Agents are for cross-cutting workflows, not tool-specific patterns. PowerShell is an implementation detail, not a persona.

## Implementation Steps (Option A)

1. **Create `common/COMMON-POWERSHELL.md`**
   - Extract reusable patterns from current `fabric-powershell-cli/SKILL.md`
   - Follow COMMON-CLI.md structure
   - Keep it focused on patterns, not complete implementations

2. **Update existing endpoint skills**
   - Add "PowerShell Alternative" sections to:
     - `powerbi-authoring-cli/SKILL.md`
     - `sqldw-authoring-cli/SKILL.md`
     - `spark-authoring-cli/SKILL.md`
   - Reference COMMON-POWERSHELL.md in Prerequisite Knowledge

3. **Deprecate `fabric-powershell-cli`**
   - Delete or repurpose as a reference example
   - Content moves to common/ and endpoint skills

4. **Update CHANGELOG.md**
   - Document the new common/ addition
   - Note the skill updates

## Content Mapping

| Current Location | New Location |
|------------------|--------------|
| Token acquisition | `common/COMMON-POWERSHELL.md` § Authentication |
| REST API patterns | `common/COMMON-POWERSHELL.md` § REST API |
| Pagination/LRO | `common/COMMON-POWERSHELL.md` § Patterns |
| CI/CD examples | `common/COMMON-POWERSHELL.md` § CI/CD |
| Workspace operations | `powerbi-authoring-cli/SKILL.md` (add PowerShell section) |
| Semantic model operations | `powerbi-authoring-cli/SKILL.md` (add PowerShell section) |
| Lakehouse operations | Future: `lakehouse-authoring-cli/SKILL.md` |
| Capacity management | `agents/FabricAdmin.agent.md` (if cross-cutting) |

## MVP Renewal Impact

This approach still counts for MVP renewal:
- Contribution to Microsoft first-party repo ✅
- Fills a gap (PowerShell patterns missing from common/) ✅
- Helps existing skills cover more access methods ✅

## Next Steps

1. Review this plan with James
2. Draft `common/COMMON-POWERSHELL.md`
3. Identify which endpoint skills to update first
4. Submit PR with common/ addition + skill updates
5. Delete or repurpose `fabric-powershell-cli`
