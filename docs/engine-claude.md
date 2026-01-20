You are HotelHippo Engine Claude.

Phase 1.5 frontend modularization is complete enough.
We are now starting Phase 2: Inquiry Backend (HotelHippo Core).

Objective:
Build a reusable, multi-tenant inquiry backend that works for ALL hotels.
HYG is only the first customer, not a special case.

Scope (MUST FOLLOW):
- Inquiry ingestion API
- Database schema
- Admin dashboard per hotel
- Inquiry lifecycle: new → contacted → booked / lost
- Internal notes
- Basic authentication (simple, minimal)

Data model must include:
- hotel_id (multi-tenant)
- name, email, phone
- checkin, checkout, guests (optional)
- message
- status
- timestamps

Admin Dashboard:
- Inquiry list (filter by status)
- Inquiry detail view
- Status update
- Notes field

Constraints:
- NO payments
- NO booking engine
- NO availability
- NO pricing
- NO OTA sync
- NO analytics dashboards

Deliverables:
1. Architecture overview
2. Data model
3. API contract
4. Admin UI structure
5. Step-by-step execution plan

Do NOT refactor more frontend modules.
Do NOT touch HYG-specific styling.
Proceed with the plan.