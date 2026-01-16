# Zunkire Hospitality Website Engine — MODULE SPECIFICATION TEMPLATE

Version: 1.0.0  
Status: LOCKED (Phase 1.5 – Module Governance)

---

## 1. MODULE IDENTITY

Each module in the Zunkire Hospitality Website Engine is an **isolated, optional capability** that can be enabled or disabled per project.

A module:
- Adds a specific feature or section
- Is fully self-contained
- Can be evolved independently
- Must never affect core stability

Modules are **consumers of engine data**, not owners of engine behavior.

---

## 2. REQUIRED FILE STRUCTURE

Every module MUST follow this structure:


modules/
└── /
├── .liquid
├── .css
└── .js   (optional)

Rules:
- The module owns all files inside its folder
- No module may write files outside its folder
- No shared JS files between modules

---

## 3. MODULE ACTIVATION RULE (MANDATORY)

A module MUST render ONLY when explicitly enabled.

### Activation Source
`site.json`

Example:
```json
{
  "engine": {
    "modules": {
      "<module-name>": true
    }
  }
}

Rules:
	•	Missing flag = disabled
	•	Disabled module MUST:
	•	Render no HTML
	•	Execute no JavaScript
	•	Apply no CSS side effects

⸻

4. DATA CONTRACT

Each module MUST define its data contract explicitly.

Required Data
	•	Field name
	•	Type
	•	Description

Optional Data
	•	Field name
	•	Type
	•	Default behavior when missing

Rules:
	•	All content must come from JSON
	•	No hardcoded text in templates
	•	Missing required data → render nothing
	•	Missing optional data → safe fallback

⸻

5. FAILURE BEHAVIOR

Modules MUST fail gracefully.

If disabled:
	•	No output
	•	No console logs
	•	No errors

If data is missing or malformed:
	•	Fail silently
	•	Do not break the page
	•	Do not affect other modules

⸻

6. ISOLATION RULES (NON-NEGOTIABLE)

Modules MUST NOT:
	•	Depend on other modules
	•	Read or modify another module’s data
	•	Mutate global state
	•	Assume execution order
	•	Assume page placement

Modules MUST:
	•	Operate independently
	•	Be safe to remove at any time

⸻

7. CSS RULES
	•	Module CSS must be scoped to the module
	•	Use engine CSS variables and tokens
	•	No global resets
	•	No overrides of core styles
	•	No assumptions about other modules’ styles

⸻

8. JAVASCRIPT RULES

If JavaScript is required:
	•	Scope JS to the module only
	•	Guard execution behind activation check
	•	Avoid global variables
	•	Avoid shared state
	•	No backend calls unless explicitly approved later

⸻

9. ACCESSIBILITY REQUIREMENTS

Every module MUST:
	•	Use semantic HTML
	•	Be keyboard navigable
	•	Use ARIA attributes where appropriate
	•	Maintain readable contrast using design tokens

Accessibility is not optional.

⸻

10. VERSIONING & CHANGE IMPACT

Changes to modules follow engine versioning rules:
	•	Breaking changes → MAJOR version bump
	•	New optional behavior → MINOR version bump
	•	Bug fixes → PATCH

Modules must not introduce breaking changes silently.

⸻

11. AI AGENT COMPLIANCE

Any AI coding agent working on modules:
	•	MUST read this file before coding
	•	MUST comply with all rules
	•	MUST refuse to write code that violates this spec

Failure to comply invalidates the change.

⸻

12. AUTHORITY

This document is authoritative for all module design and implementation.

If:
	•	Code
	•	Documentation
	•	AI output

conflicts with this specification →

THIS FILE WINS.