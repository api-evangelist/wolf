---
name: wolf-job-seeker-shift-flow
description: Sign a job seeker in, find available shifts, apply, and check in/out on the Wolf staffing platform.
api: Wolf Staffing API
generated: '2026-07-21'
method: generated
source: https://docs.fromwolf.com/llms.txt
base_url: https://api.wolf.xyz/api/v1/
operations:
- sign_in
- view_all_jobs
- new_opportunities
- shift_update_status
- my_calendar_schedule
---

# Wolf — Job Seeker Shift Flow

Coordinate a freelancer (job seeker) working shifts through the Wolf multi-tenant
staffing API. Every request needs the tenant key plus a credential (see the auth
rules below).

## Auth & conventions (read first)
- Send the `tenant` key on every call (staffing-company identifier, often ends `_db`).
- Send the `Authentication` header. For user actions use the user's
  `authentication_token`; for tenant-level reads include the platform API key.
- Errors come back as `{ status, data: { opertaion_status }, message }` — NOT
  RFC 9457. See `errors/wolf-problem-types.yml`.
- No idempotency keys; state changes are explicit status-update calls.

## Steps
1. **Authenticate** — `POST /sign_in` with `email`, `password`, `tenant` (and
   optional `one_time_password`, `os_type`, `device_id`, `latitude`,
   `longitude`). Store `user.authentication_token`. The user's `user_type` must
   be `freelancer` to access jobs.
2. **List available work** — `GET /shifts/view_all_jobs` (available & upcoming)
   and `GET` new opportunities for scoped, targeted shifts.
3. **Check the calendar** — `GET /shifts/my_calendar_schedule` to see scheduled
   shifts, auto-availability and blocked days before committing.
4. **Act on a shift** — `POST /shifts/shift_update_status` to Apply, Reconfirm,
   Cancel, Check-in, or Check-out of a specific shift.
5. **Confirm** — re-read the calendar / tender status to verify the new state.

## Failure handling
- `401` (`Failed authorizing user`): re-run `sign_in`; confirm tenant + token
  match and `user_type == freelancer`.
- `400` (`Missing Parameters`): ensure `tenant` and required body fields are set.
