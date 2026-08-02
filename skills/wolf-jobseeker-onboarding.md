---
name: wolf-jobseeker-onboarding
description: Create/update a job seeker profile and complete onboarding, credentials, preferences and bank connection on Wolf.
api: Wolf Staffing API
generated: '2026-07-21'
method: generated
source: https://docs.fromwolf.com/llms.txt
base_url: https://api.wolf.xyz/api/v1/
operations:
- create_or_update_jobseeker
- get_custom_onboarding
- get_credentials_requirements
- jobseeker_jobtype_preferences
- bank_connection_setup_status
---

# Wolf — Job Seeker Onboarding

Bring a new freelancer onto a Wolf tenant: profile, custom onboarding answers,
credentials, job-type preferences, and payout (bank) connection.

## Auth & conventions (read first)
- Include the `tenant` key on every call.
- Profile writes act on behalf of the user — send the user `authentication_token`
  in the `Authentication` header. Include the platform API key for tenant reads.
- See `conventions/wolf-conventions.yml` and `authentication/wolf-authentication.yml`.

## Steps
1. **Create/update the profile** — `POST /create_or_update_jobseeker` with the
   job seeker's profile fields.
2. **Custom onboarding** — `GET /jobseeker/get_custom_onboarding` to retrieve the
   tenant's onboarding questions, then submit the answers back to the same
   surface.
3. **Credentialing** — `GET /jobseeker/get_credentials_requirements` and satisfy
   the required credentials for the tenant.
4. **Preferences** — `GET`/`POST /shifts/jobseeker_jobtype_preferences` to read
   available job types and set the seeker's preferences.
5. **Education & experience** — add/update via the education & experience surface.
6. **Payouts** — `GET /jobseeker/bank_connection_setup_status`; connect the bank
   account via Stripe when not yet set up.

## Failure handling
- `400` (`Missing Parameters`): confirm `tenant` and all required profile fields.
- `401`: refresh the user token via `sign_in`.
