# Phase 2: HotelHippo Core - Inquiry Backend

> **Status:** Implementation Ready
> **Backend:** Supabase
> **Repository:** `hotelhippo-core/`

---

## Overview

HotelHippo Core is the multi-tenant inquiry backend for all hotel websites using this engine. It uses Supabase as the backend platform.

**Location:** `../hotelhippo-core/`

---

## Architecture

```
┌──────────────────┐     ┌──────────────────┐
│  Hotel Websites  │     │  Admin Dashboard │
│  (inquiry-form)  │     │  (static HTML)   │
└────────┬─────────┘     └────────┬─────────┘
         │                        │
         │ Supabase JS client     │
         └────────────┬───────────┘
                      │
                      ▼
         ┌────────────────────────┐
         │       SUPABASE         │
         │                        │
         │  - PostgreSQL + RLS    │
         │  - Auth (email/pass)   │
         │  - REST API            │
         └────────────────────────┘
```

---

## What Supabase Provides

| Feature | Description |
|---------|-------------|
| Database | Managed PostgreSQL |
| Auth | Email/password login for admins |
| API | Auto-generated REST endpoints |
| Security | Row Level Security for multi-tenancy |
| Hosting | Fully managed, free tier available |

---

## Data Model

### Tables

- **hotels** - Multi-tenant registry (HYG, Lapland, etc.)
- **users** - Admin accounts linked to Supabase Auth
- **inquiries** - Guest inquiries with status lifecycle
- **inquiry_notes** - Internal staff notes

### Status Workflow

```
new → contacted → booked
                → lost
```

---

## Admin Dashboard

Static HTML/CSS/JS dashboard at `hotelhippo-core/admin-ui/`:

- **Login** - Supabase Auth email/password
- **Inquiry List** - Filter by status, search, pagination
- **Inquiry Detail** - Full info, status update, notes

---

## Frontend Integration

The `inquiry-form` module will need to be updated to submit to Supabase:

```javascript
// In inquiry-form module
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

await supabase.from('inquiries').insert({
    hotel_id: 'uuid',
    name: formData.name,
    email: formData.email,
    // ...
});
```

---

## Setup Instructions

See `hotelhippo-core/README.md` for:

1. Creating Supabase project
2. Running migrations
3. Creating admin users
4. Configuring the dashboard

---

## Constraints (Unchanged)

This system does NOT include:

- Payment processing
- Booking/reservation engine
- Room availability
- Pricing rules
- OTA channel sync
- Analytics dashboards
- Email/SMS notifications

---

## Next Steps

1. Create Supabase project
2. Run migration SQL
3. Create test admin user
4. Test admin dashboard locally
5. Update inquiry-form module for Supabase integration
6. Deploy to production

---

*See `hotelhippo-core/` for full implementation.*
