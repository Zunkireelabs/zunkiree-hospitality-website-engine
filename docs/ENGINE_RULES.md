# Zunkire Hospitality Website Engine — ENGINE RULES

Version: 1.0  
Status: Locked (Phase 1.5)

---

## 1. ENGINE IDENTITY

This repository is a **product-grade static website engine** for hospitality businesses.

It is:
- Static-first
- Template-governed
- Data-driven
- Multi-project safe
- SaaS-evolvable

It is NOT:
- A CMS
- A backend system
- A page builder
- A React/Vue app
- A client-specific codebase

---

## 2. LAYER RESPONSIBILITIES (NON-NEGOTIABLE)

### Engine Layer (THIS REPO)
Allowed:
- Core layout
- Shared components
- Module definitions
- CSS design tokens
- Module contracts
- Engine versioning

Forbidden:
- Client content
- Domain-specific hacks
- Backend logic
- Auth, DBs, APIs
- Pricing logic
- CMS/editor logic

---

### Project Layer (e.g. HYG)
Allowed:
- Content (JSON)
- Media
- Page composition
- Domain config
- Module enable/disable

Forbidden:
- Modifying engine internals
- Forking engine logic
- Changing core behavior

---

## 3. CORE vs MODULES

### CORE
- Always present
- Never optional
- Never removed
- Never depends on modules

Includes:
- base layout
- header
- footer
- global CSS foundations

---

### MODULES
- Optional
- Explicitly enabled
- Fully isolated
- Self-contained

Rules:
- Modules may NOT depend on other modules
- Modules may NOT mutate global state
- Modules must fail gracefully when disabled
- Modules own their CSS and JS

---

## 4. MODULE ACTIVATION CONTRACT

Modules are enabled via `site.json`:

```json
{
  "engine": {
    "version": "1.0.0",
    "modules": {
      "hero": true,
      "inquiry": false,
      "availability": false,
      "video": true
    }
  }
}


Rules:
	•	Missing flag = disabled
	•	Disabled module renders nothing
	•	No JS should execute if disabled

⸻

5. DATA CONTRACT RULES
	•	All content comes from JSON
	•	No hardcoded text in templates
	•	Fields may be added in v1
	•	Fields may NOT be removed in v1
	•	Breaking changes require MAJOR version bump

⸻

6. VERSIONING RULES

Semantic Versioning:
	•	MAJOR → breaking change
	•	MINOR → new module or optional feature
	•	PATCH → bugfix only

No breaking change without MAJOR bump.

⸻

7. FORBIDDEN ACTIONS

Explicitly forbidden:
	•	Adding module logic to core
	•	Cross-module dependencies
	•	Hardcoding content
	•	Introducing backend features
	•	Adding CMS/editor logic
	•	Writing project-specific code here

If a change violates these rules → DO NOT IMPLEMENT.


---

# 📐 2️⃣ MODULE INTERFACE TEMPLATE (MANDATORY)

> **Purpose:** Every module MUST follow this structure.

```md
# Module Specification Template

Module Name: <module-name>  
Status: Optional  
Type: Isolated Module  

---

## 1. PURPOSE
Clear description of what this module does.

---

## 2. DATA CONTRACT

Source: `<json file>`

Required:
- <field>: <type>

Optional:
- <field>: <type> (default behavior described)

Module must render nothing if required data is missing.

---

## 3. ACTIVATION RULE

Module renders ONLY IF:
```json
{
  "engine": {
    "modules": {
      "<module-name>": true
    }
  }
}

4. FILE OWNERSHIP

The module MUST own:
	•	<module-name>.liquid
	•	<module-name>.css
	•	<module-name>.js (if needed)

No shared JS with other modules.

⸻

5. FAILURE BEHAVIOR

If disabled:
	•	No HTML output
	•	No JS execution
	•	No CSS side effects

If data missing:
	•	Fail silently
	•	No console errors

⸻

6. ACCESSIBILITY
	•	Semantic HTML
	•	Keyboard accessible
	•	ARIA where required

⸻

7. NON-FUNCTIONAL
	•	No global state mutation
	•	No dependency on other modules
	•	No assumptions about page order
