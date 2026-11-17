# Follow swipe

A one-page app for cleaning up who you follow on Instagram. It reads Instagram's own data export, shows every account you follow that doesn't follow you back as a card, and lets you swipe: left to drop, right to keep, up to keep and browse later. Drops collect in an Unfollow list where each name opens the profile in Instagram, and you unfollow there yourself.

Live at https://bar-katz.github.io/ig-swipe/ (GitHub Pages, served from `main`).

## Use

1. In Instagram: Accounts Center → Your information and permissions → Download your information → Some of your information → **Followers and following**, format **JSON**, date range **All time**. Instagram emails when the zip is ready, usually within minutes.
2. Open the page in Chrome on the phone and load the zip. Add it to the home screen for one-tap access.
3. Swipe. Each card has a profile button (top right) and a **Not found** button for accounts whose profile no longer opens.
4. Unfollow tab: tap a name, unfollow in Instagram, come back. Tapped names are crossed off.
5. Later, load a newer export from the Unfollow tab. Your swipes carry over (they're keyed by username), and drops that are no longer in your following list are crossed off on their own.

## Data

- Everything runs in the browser. The zip is read on the phone and nothing is uploaded; the page makes no network requests besides loading itself.
- State lives in the browser's `localStorage` for this site: `follows` (from the export), `swipe` (decisions), `groups` (labels from an imported CSV). Clearing the browser's site data, using Incognito or a different browser starts from nothing.
- **Download spreadsheet (CSV)** is the backup. **Import progress (CSV)** reads it back on any phone or browser: decisions, plus the `tier` and `group` columns as card labels and deck order. The page never writes group labels itself; the first deck's groups were sorted by hand from usernames and travel only through the CSV.

## What the deck contains

- Accounts in `following.json` that are not in any `followers_N.json`, oldest follow first (or in the imported CSV's order).
- Accounts that follow you back are left out unless you already swiped them.
- Instagram's `__deleted__…` placeholders are skipped.

## Rules this keeps to

- Instagram's terms forbid scraping profiles and automating account actions. The page only records decisions and links to `instagram.com/<username>`; every unfollow is a manual tap in Instagram. Don't add profile fetching, avatars or auto-unfollow.
- Unfollow in batches. Instagram can temporarily block unfollowing when it happens too fast.

## Export format notes

- `connections/followers_and_following/following.json` is `{"relationships_following": [{"title": "<username>", "string_list_data": [{"href": ".../_u/<username>", "timestamp": <unix>}]}]}`.
- `followers_1.json`, `followers_2.json`, … are lists of `{"string_list_data": [{"href", "value": "<username>", "timestamp"}]}`; large accounts get several files.
- `recently_unfollowed_profiles.json` has your recent unfollows with timestamps; the newest one shows roughly when the export was taken.
- The export still lists deactivated or renamed accounts that the profile counter leaves out, so the export's following count can be higher than the app's. Those are the "Not found" cards.

## Develop

Single file, no build: `index.html` (HTML, CSS and script). Run it locally with `python3 -m http.server 8000` and open http://127.0.0.1:8000. The zip reader uses the zip central directory and `DecompressionStream('deflate-raw')`, so it needs a current Chrome. Deploy by pushing to `main`.

Known quirk: in Chrome, a button tap within about a quarter second of a very fast flick can be swallowed (Chrome treats it as stopping a fling). It doesn't happen at normal swipe speed.
