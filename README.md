# My Kanban Board

A personal kanban board (Now / Next / Waiting on others / Done) with
work/personal tags, size labels (big rock / medium / small), and Google
sign-in protecting the whole app.

## How it works

- The board UI lives in `site/` (`index.html`, `styles.css`, `app.js`).
- `build.js` inlines those files into `api/index.js` / `api/login.js` so every
  page is served through a serverless function that checks the session cookie.
- Google sign-in via Google Identity Services: `/login` renders the Google
  button, which returns an ID token; `POST /api/session` verifies the token's
  RS256 signature against Google's public certs, checks the audience, expiry,
  and that the email is in `ALLOWED_EMAILS`, then sets a signed `HttpOnly`
  session cookie (`kb_session`). No client secret is needed or stored.
- `GET /api/config` exposes the public client ID to the login page.
- Card data persists in the browser's `localStorage`, seeded from the seed
  array in `site/app.js` on first load.

## Environment variables (set in Vercel, never in git)

| Variable             | Purpose                                                  |
| -------------------- | -------------------------------------------------------- |
| `GOOGLE_CLIENT_ID`     | Google OAuth web client ID (public; shown on login page) |
| `SESSION_SECRET`       | Random string used to sign session cookies               |
| `ALLOWED_EMAILS`       | Comma-separated emails allowed to sign in                |

The Google OAuth client's **authorized JavaScript origin** must be
`https://<your-vercel-domain>` (no redirect URI needed).

## Local build

```sh
node build.js   # regenerates api/index.js and api/login.js from site/
```
