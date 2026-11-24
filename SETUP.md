# Field Sales Tracker — Cloud Setup

This is the cloud-ready CRM frontend, Supabase database schema, and SMS-verified password reset function. It is not hosted yet; a Supabase project and hosting account must be configured.


## 1. Create Supabase project
1. Create a Supabase project at https://supabase.com.
2. Open SQL Editor and run `supabase/schema.sql`.
3. Go to Project Settings → API and copy Project URL and anon/public key.
4. Copy `config.example.js` to `config.js` and fill in these values.

## 2. Authentication settings
1. In Authentication → Providers, enable Email provider (password).
2. Disable public sign-ups.
3. Enable Phone/SMS provider and configure an SMS gateway/provider.
4. For accounts, use employee-ID aliases as auth email: `<employeeid>@fieldcrm.team`. This alias is only an auth identifier; no email inbox is needed for password login.
5. Provision each Auth user with their chosen initial password and phone number. Set their phone as confirmed only after verifying it during onboarding.
6. Create matching row in `public.profiles` using Auth user's UUID, employee_id, display_name, phone, role (`sales`, `manager`, `admin`) and `is_active=true`.
7. The first manager profile should be manually assigned role `manager` or `admin` in the Supabase SQL editor.

## 3. Deploy password reset function
From a local machine with Supabase CLI installed and logged in:

```bash
supabase functions deploy reset-password
```
The function uses Supabase environment variables automatically, including service role key. Do not expose the service-role key in the browser.

## 4. Host the app
Host the folder on a static hosting service such as Netlify, Vercel, or Cloudflare Pages. Ensure `config.js` is included in the deployment. Publish the resulting HTTPS URL and share it with the team.

## 5. Role behavior
- Sales users see only their own visit and opportunity records; customer master is shared.
- Manager/Admin can view team-wide visit and opportunity data.
- User creation and role changes are intentionally admin-only (Supabase dashboard) in this starter build.
- Customer master records are shared and protected by row-level security.

## 6. Data export
The UI exports CSV files for Customer Master, Visit History, and New Business. Excel opens CSV directly. Manager exports are team-wide; sales users export their permitted records.

## 7. Production checklist
- Add a unique constraint/duplicate detection rule on normalized customer name + city if desired.
- Add server-side validation for financial values and opportunity status changes.
- Set Supabase backup/retention and monitor SMS usage/costs.
- Configure allowed redirect URLs and HTTPS host domain.
- Test password reset and account recovery with a real SMS number before rollout.
