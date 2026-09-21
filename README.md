# OneScan: go live in about 30 minutes

## 1. Supabase
1. Create a project at supabase.com.
2. SQL Editor: paste and run `schema.sql`.
3. Authentication > Providers > Email: keep "Confirm email" on. In Authentication > Email Templates,
   edit the "Magic Link" template so it shows the code: use `{{ .Token }}` in the body.
   (The code length is set under Authentication > Providers > Email. The app accepts up to 10 digits.)
4. Project Settings > API: copy the Project URL and the anon key.
5. Turn on backups (Project Settings > Database).

## 2. Edge functions (needs the Supabase CLI: `npm i -g supabase`)
```
supabase login
supabase link --project-ref YOUR_PROJECT_REF
supabase secrets set ANTHROPIC_API_KEY=sk-ant-...
supabase functions deploy create-share
supabase functions deploy open-share --no-verify-jwt
supabase functions deploy summarize
supabase functions deploy extract-report
supabase functions deploy delete-account
```
Run these from the `onescan` folder (it contains `supabase/functions`).

## 3. Frontend
1. Open `index.html`, replace `PASTE_PROJECT_URL` and `PASTE_ANON_KEY` (anon key only, never the service key).
2. Deploy the folder's `index.html` on Vercel or Netlify (drag and drop works).
3. In Supabase: Authentication > URL Configuration, set Site URL to your deployed address.

## 4. Test before inviting anyone
- Sign up with your email, add a record from a photo, generate both QR types.
- Open the QR link in a private window: the emergency link shows no records, the full link asks for the code.
- Enter a wrong code 5 times: the link locks.
- Delete a test account and confirm its rows and photos are gone.
- Start with 5 to 10 people you know.

## Known limits
- Email OTP for login. SMS needs DLT registration in India; add later.
- The app logic was tested with a mocked Supabase, not a live project. Step 4 is the real test; send me any error you see.
- Wrong-code attempts are limited to 5 per QR, but a very fast burst of parallel guesses could slip a few extra through. Acceptable for a pilot; tighten with a database function before wide launch.
- Add a privacy policy page before public launch.
