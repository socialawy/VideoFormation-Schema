# Security

## Schema Safety

This schema is validated against:
- Regex patterns tested for ReDoS resistance
- No recursive definitions that could cause parsing bombs

## Constraints table:
```markdown
## Field Constraints

| Field | Pattern | Example |
|-------|---------|---------|
| `project_id` | `^[A-Za-z0-9_-]+$` | `MY_PROJECT_01` |
| `created_date` | `YYYY-MM-DD` | `2026-02-11` |
| `sequence_id` | `^SEQ\d+$` | `SEQ01` |
| `scene_id` | `^S\d+$` | `S01` |
| `shot_id` | `^SEQ\d+\.S\d+\.SH\d+$` | `SEQ01.S01.SH01` |
| `language` | ISO 639-1 | `en`, `en-US` |
| `color` | Hex | `#FFFFFF` |
```

## Pattern Type | Examples | ReDoS Risk
| Pattern Type | Examples | ReDoS Risk |
| --- | --- | --- |
| Fixed format | `^\d{4}-\d{2}-\d{2}$` (date), `^#[0-9A-Fa-f]{6}$` (hex) | ✅ None
| Simple IDs | `^SEQ\d+$`, `^[A-Za-z0-9_-]+$` | ✅ None
| Locale | `^[a-z]{2}(-[A-Z]{2})?$` | ✅ None
| Enum + pipe | `^(draft\|in_progress)` | ✅ None

**Note: No nested quantifiers — The classic ReDoS pattern (a+)+ or (a\|aa)+ is absent. All patterns are linear.**

## Usage Guidelines

- **Do not** store credentials, API keys, or secrets in blueprint.json files
- Validate untrusted input before processing
- Use pinned schema versions in production

- **Do not** store credentials, API keys, or secrets in blueprint files
- Validate untrusted input before processing
- Use pinned schema versions (e.g., `v1.1.0.json`) in production

## Reporting Vulnerabilities

Report vulnerabilities via [GitHub Security Advisories](https://github.com/socialawy/VideoFormation-Schema/security/advisories/new)
