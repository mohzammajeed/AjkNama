# AJK Nama

Community website for all of Azad Jammu & Kashmir (AJK): live weather, prayer times, local news, photos, events, notices, businesses (shops, hotels, taxis, fruit and vegetables) and places (visiting spots, mosques, schools, hospitals). Static site on Vercel, Firebase (Spark plan) for data.

## Files
| File | Purpose |
|---|---|
| `index.html` | The whole public site (inline CSS and JS) |
| `admin.html` | Admin panel at `/admin`: delete any post (Google sign-in) |
| `firestore.rules` | Firestore security rules (paste into Firebase console) |
| `api/weather.js` | Serverless function: calls Open-Meteo with your private API key |
| `vercel.json` | Clean URLs (`/admin`) and security headers |
| `manifest.webmanifest` | Lets people install the site on their phone home screen |
| `sw.js` | Service worker: opens the app shell offline |
| `offline.html` | Shown when the app can't open and nothing is saved |
| `404.html` | Not-found page |
| `icons/`, `assets/` | Your logo as favicon, app icons (192/512/1024), maskable and Apple icons, plus the original logo |
| `robots.txt` | Keeps `/admin` out of search |

## Setup
1. **Firestore:** create the database, then paste `firestore.rules` into Rules and Publish.
2. **Authentication:** enable the **Google** and **Email/Password** providers. Add your Vercel domain (and custom domain) under Settings → Authorized domains.
3. **GitHub:** push all files to the repo root.
4. **Vercel:** import the repo. Framework preset: Other. No build command, no output directory.
5. Open `/admin`, sign in with the admin Google account, and delete any post.

## User accounts
Users can change their name, request a password reset email, delete all their own posts, or delete their account (which also deletes their posts). They can only touch their own data; the Firestore rules enforce this.

## Comments, refresh, offline
Signed-in users can comment on posts (stored in the `comments` collection; users delete their own, the admin can delete any). Pulling down at the top of a page refreshes the feed and weather. When offline the site shows the last saved weather and posts (without photos).

## PWA (installable web app)
- **Get the app:** on Android a popup (once per visit) and an animated banner slide offer the APK download (`/AJKNama.apk`). Put `AJKNama.apk` in the repo root next to `index.html`; replace the file to publish an update. If the file is missing, Android visitors get the normal browser install prompt instead. iPhone visitors see "Share → Add to Home Screen". Nothing is shown inside the installed app.
- **Home-screen shortcuts:** long-press the icon for New post, Business, Places, Weather.
- **Share to AJK Nama:** on Android, sharing text or a link from another app can open the new-post window with it filled in.
- **Offline:** the app opens without internet; the last weather and posts are shown. Firebase scripts and fonts are saved after the first visit.
- **Updates:** when you deploy a new version, people see "A new version is ready. Reload".
- Test with Chrome DevTools → Lighthouse → Progressive Web App, and Application → Manifest.

## App feel
Splash screen, bottom tab bar, slide-in pages, press feedback, safe-area padding for notches, Android back button closes windows before leaving, pull-to-refresh, offline mode. The admin link now lives in the account window (visible only to the admin).

## Social features
- **Likes:** shared like counts. Each like is one document in `likes` (`postId_uid`) and the post's `likeCount` changes in the same batch; the rules only allow +1/-1 together with that document.
- **Profiles:** tap any name or picture to open a public profile (`users` collection: name, photo, bio, member since). Own profile has Posts and Saved tabs.
- **⚙️ Settings:** one gear (header and own profile) holds profile edit, dark mode, password email, sign out, delete all posts, delete account.
- **⋯ post menu:** Save, Share, Edit/Delete (own posts), Report (others); admin can delete any post.
- **Reports:** stored in `reports` (admin only). Review them in `/admin` → Reports.
- Search box and 🔥 Top (most liked) on the Home feed.

## Layout (v4)
- **Bottom bar:** Home, Business, Places, Weather, More. Nothing else is repeated in the header.
- **More** holds everything personal: sign in / profile card, Ask AJK Nama AI, My profile, Account settings, dark mode, install app and (admin only) the admin panel.
- **Home** is one feed of everything: news, photos, events, notices, businesses and places. Filter chips: All, News, Business, Places, Photos, Events, Notices, Top. The round shortcuts under the weather card jump straight to a category.
- **Business** and **Places** pages show every category as a tile with a count, a search box, and full listings (name, category, details, address, Call button, photo).

### Categories
Edit the `HUBS` block in `index.html`: `[name, icon, short label]`.
- Business: Shops, Hotels & Stay, Restaurants & Cafes, Taxi & Transport, Fruits & Vegetables, Farm & Dairy, Repair & Trades, Pharmacy, Mobile & Electronics, Tailors & Fashion, Professional Services, Other Services
- Places: Visiting places, Mosques, Schools, Hospitals & Clinics, Public offices

### Listing your business or service
Signed-in users tap **List yours** (Business) or **Add** (Places). The form asks for name, details, category, address, phone, WhatsApp, opening hours and a photo. Each listing shows Call, WhatsApp and Directions buttons. Owners can edit their listing from the ⋯ menu.

Listings are stored in the existing `directory` collection (the `cat` field decides Business or Places). New fields: `addr`, `whatsapp`, `hours`. **Republish `firestore.rules`** so owners can edit those fields; creating listings works with the old rules too. Old entries with categories like `Clinics`, `Shops`, `Transport` or `Electrician` are mapped automatically.

## Languages: English and Urdu
- The **اردو / English** button in the header switches the whole site (right-to-left layout, Urdu font, Urdu weather and prayer wording). The choice is remembered. First-time visitors whose phone is set to Urdu see Urdu automatically.
- All Urdu text lives in `ur.js`. It is only downloaded when someone picks Urdu. To change a translation, edit the matching line. Change `NAME` at the top of `ur.js` if you want the town's name written in Urdu script.
- Text typed by users (posts, business names, comments) is shown exactly as written.


## Icons
All symbols in the site are inline SVG icons (one sprite at the top of `index.html`), not emojis. The logo icon is used for the favicon, header, splash screen, home-screen install and the future mobile app (`icons/icon-1024.png`, `icons/maskable-512.png`).

## Settings
Edit the `CFG` block near the top of the script in `index.html`:
- `lat`, `lon`, `city`: location used for weather, air quality and prayer times (now Muzaffarabad, the AJK capital)
- `region`: text shown above the title
- `prayerMethod`: Aladhan calculation method (1 = Karachi)

## How posts work
Visitors must sign in (Google or email) to post, using the post box at the top of the feed. Photos are shrunk in the browser (max 900 px JPEG) and stored inside the Firestore document. Posts appear immediately with the poster's name. Users can delete their own posts (🗑 Delete button); the admin can delete any post from `/admin`.

## Weather API key
In Vercel → Settings → Environment Variables add `OPEN_METEO_API_KEY` (all environments), then redeploy. The site calls `/api/weather`; if that fails it falls back to the free Open-Meteo endpoint.

## Never commit
Service account keys and API keys that must stay private. Put them in Vercel → Settings → Environment Variables. The `.gitignore` already skips `.env` and service account files. The Firebase web config in `index.html` is public by design; the Firestore rules are what protect your data.


## Search visibility (SEO)
The home page has a title, description, keywords, canonical link, Open Graph and Twitter cards (`assets/og-image.png`), JSON-LD (`WebSite` with alternate names AJKNama and اے جے کے نامہ, plus `Organization`), a `<noscript>` text fallback, `robots.txt` and `sitemap.xml`.

**Your domain.** Every address is currently `https://ajknama.vercel.app`. When you buy a custom domain (for example `ajknama.com`), connect it in Vercel → Settings → Domains, then replace the old address everywhere with one command from the repo folder:
`grep -rl "ajknama.vercel.app" --exclude-dir=.git . | xargs sed -i 's#ajknama.vercel.app#ajknama.com#g'`
Also add the new domain in Firebase → Authentication → Settings → Authorized domains, or Google sign-in will fail.

**After each deploy**
1. Google Search Console → add the site → paste the verification tag into the marked comment in `index.html` → submit `/sitemap.xml`.
2. Bing Webmaster Tools → import from Search Console.
3. Use the exact same name "AJK Nama" for Facebook, Instagram, YouTube and WhatsApp channel pages and link them to the site.

## Weather by location
The weather page asks once for your device location (browsers need HTTPS, which Vercel provides). If it is blocked, or the visitor prefers, there is a list of AJK towns (`PLACES` in `index.html`; add more as `[name, lat, lon]`). The choice is saved in the browser (`ajk_loc`) and is never written to Firestore. Coordinates are rounded to 2 decimals so Vercel can cache forecasts (`/api/weather` uses your private `OPEN_METEO_API_KEY`).
