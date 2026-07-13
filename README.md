# Volli marketing site

Static marketing site for **Volli** (an app to find and book pickleball/tennis/basketball
courts), served via **GitHub Pages** from the `main` branch. No build system, package manager,
or test suite — plain HTML plus image assets. Tailwind is loaded at runtime via CDN.

Pages: `index.html` (Home) and `about.html` (About). There are no shared/partial files, so
shared markup (nav, palette, and the email popup) is duplicated in both pages and must be kept
in sync — see `CLAUDE.md`.

## Deploy

Push to `main`; GitHub Pages serves the repo root. There is no build step.

## Run / preview locally

Open the `.html` files in a browser for a quick look, **or** — recommended — serve them so the
site runs on a real `http` origin:

```sh
python3 -m http.server 8000
```

Then visit <http://localhost:8000/>.

Prefer the server: opening pages as `file://` does **not** reproduce production behavior for
anything that relies on cookies or `localStorage` (see the popup notes below).

## Email-capture popup

Both pages show a first-visit popup (~4s after load) that collects an email plus a
Player/Facility choice and submits to **MailerLite** (subscribers land in the account's list).
It appears **once per visitor across the whole site**, tracked with the `volli_email_popup_seen`
flag stored in both `localStorage` and a domain-wide cookie (the cookie also covers
private-browsing modes where `localStorage` writes can fail silently).

### How to test it

Serve the site with `python3 -m http.server` and visit `http://localhost:8000/` — **do not**
just open `index.html` as a file. Over `file://`, cookies are ignored and `localStorage` is not
reliably shared between the two pages, so the "show once" gating will misbehave in ways that do
**not** happen on the live `https` site.

1. **First visit:** open `http://localhost:8000/` in a fresh Incognito/Private window → the
   popup appears after ~4 seconds.
2. **Return visit:** reload the page → the popup does **not** reappear.
3. **Cross-page (the important one):** dismiss the popup on Home, then navigate to
   `about.html` → it stays closed. Repeat starting from About.
4. **Dismissal paths:** each of the close (×) button, backdrop click, and `Esc` closes the
   popup and prevents it from reappearing on reload.
5. **Reset between tests:** open a new Incognito window, or in DevTools →
   Application → Storage delete the `volli_email_popup_seen` key **and** cookie, then reload.
6. **Real submission (source of truth):** submit a test email as Player and another as
   Facility, then confirm both appear in **MailerLite → Subscribers** with the correct
   `facility_or_player` value. The popup shows optimistic success (it can't read MailerLite's
   cross-origin response), so the dashboard is the only way to confirm delivery. Delete test
   entries afterward.

### MailerLite notes

- The form posts directly to the MailerLite embedded-form endpoint; there is no backend.
- **reCAPTCHA must stay disabled** on that MailerLite form, or direct submissions are rejected.
- If double opt-in is enabled, new subscribers stay "unconfirmed" until they click the
  confirmation email.
