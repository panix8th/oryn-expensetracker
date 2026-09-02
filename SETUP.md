# Oryn — Expense Tracker
## Setup & Publishing Guide

> *Know where it goes.*

---

## What's inside

A complete React Native + Expo app:

- **Oryn** branding throughout — setup screen, lock screen, settings, deep links
- Income & expense tracking with live ECB exchange rates (no API key needed)
- First-launch onboarding — name, currency picker (20 currencies), Face ID setup
- Edit / delete / view all entries with receipt photos
- Monthly/weekly/daily filters + month navigation
- Spending comparison vs previous month + projected monthly spend
- Per-category budgets with push notifications at 80% and 100%
- Recurring expenses (daily/weekly/monthly/yearly)
- Auto-categorization by merchant name
- Duplicate detection
- Notes field on every entry
- CSV export
- Face ID / Touch ID lock screen
- Dark/light mode (dark default, lilac purple accent)
- Apple Pay Shortcut: `oryn://add` deep link

---

## Step 1 — Install Node.js

1. Go to **nodejs.org**
2. Download the **LTS** version (left button)
3. Install it
4. Open Terminal and run: `node -v` → should show `v20.x.x`

---

## Step 2 — Set up the project

No global CLI install needed — `npx` runs everything from the project's own dependencies.

```bash
cd path/to/oryn
npm install
```

---

## Step 3 — Preview on your iPhone (no Mac needed)

1. Install **Expo Go** on your iPhone (free, App Store)
2. Run:
```bash
npx expo start
```
3. Scan the QR code with your iPhone camera
4. Oryn opens instantly on your phone ✓

This is great for trying things out, but Expo Go can't do Face ID, background
tasks, or install as a real standalone app icon. For that, sideload a real
build — see below.

---

## Step 4 — Sideload a real app onto your iPhone (free, no Mac, no Apple Developer account)

This builds an actual `.ipa` you install permanently on your phone with an
icon on your home screen — using nothing but a free Apple ID.

1. On GitHub, go to **Actions → Build Oryn Sideloadable IPA → Run workflow**.
   This runs entirely on GitHub's macOS runners — you don't need a Mac.
2. Wait for the run to finish (~10–15 min), then open it and download the
   **`oryn-unsigned-ipa`** artifact. Unzip it to get `oryn-unsigned.ipa`.
3. Install **[AltStore](https://altstore.io)** or **[SideStore](https://sidestore.io)**
   on your iPhone (follow their setup guide — it pairs with your free Apple ID
   the same way Xcode would).
4. Open AltStore/SideStore, choose **My Apps → +**, and select `oryn-unsigned.ipa`.
   The app signs it with your Apple ID and installs it like any other app.
5. On your iPhone: **Settings → General → VPN & Device Management** → trust
   your Apple ID developer profile the first time you launch Oryn.

**The catch with a free Apple ID:** the app's signature expires after **7
days**, after which iOS refuses to open it until you re-sign. AltStore/
SideStore can do this automatically in the background as long as the app
stays installed and your phone is on Wi-Fi periodically (SideStore can even
do it without a computer nearby; AltStore needs AltServer running on a
computer on the same network once a week). A **paid Apple Developer account**
($99/year) extends this to a full year per build and removes the weekly
refresh entirely — worth it if 7-day resigning gets annoying.

---

## Optional — Publish to the App Store instead

Only needed if you eventually want Oryn on the public App Store rather than
sideloaded for personal use.

### Accounts needed
- **Apple Developer**: developer.apple.com — $99/year
- **Expo EAS**: expo.dev — free

### Configure app.json
- `"bundleIdentifier"` is already set to `com.agit.oryn` — change it to your
  own reverse-domain identifier if you want a different one
- After running `eas init`, paste your project ID into `"projectId"`

### Build & Submit
```bash
npx eas-cli login
npx eas-cli init
npx eas-cli build --platform ios --profile preview   # builds in Expo cloud, ~10-15 min
npx eas-cli submit --platform ios                    # uploads to App Store Connect
```

### App Store Connect
1. Go to appstoreconnect.apple.com
2. Fill in description, screenshots, category: **Finance**
3. Submit for review — Apple reviews in 1–3 days

---

## Apple Pay Shortcut (automatic logging)

1. Open **Shortcuts** app on iPhone
2. Tap **Automation** → **+** → **New Automation**
3. Choose **Notification** → select **Wallet**
4. Filter: notification contains `"paid"`
5. Add action: **Open URL** → type `oryn://add`
6. Turn OFF **"Ask Before Running"**
7. Done ✓

Every Apple Pay notification now instantly opens Oryn to the Add Expense screen.

---

## Trademark next steps

Now that you've chosen **Oryn**, consider registering the trademark:

1. **Switzerland (IPI)**: ipi.ch — CHF ~800 for Classes 9 + 36
2. **EU (EUIPO)**: euipo.europa.eu — €850 for one class, covers all EU countries
3. **USA (USPTO)**: uspto.gov — ~$350/class

File in:
- **Class 9** — downloadable mobile software
- **Class 36** — financial tracking services

Recommended: file in Switzerland first (your home market), then EU. USA optional unless you plan to market there. A trademark attorney can do the full filing for ~CHF 500–1500 depending on country.

---

## App icon & assets

`assets/icon.png`, `assets/splash.png`, `assets/adaptive-icon.png`, and
`assets/notification-icon.png` are already included — a lilac-purple ring
"O" on the dark Oryn background, matching the in-app loading screen. Replace
them with your own artwork any time (same filenames/sizes: 1024×1024 for
icon/adaptive-icon, 1284×2778 for splash, 96×96 white-on-transparent for the
notification icon).

---

## Project structure

```
oryn/
├── App.js                           ← Root + biometric lock + navigation
├── app.json                         ← Expo config (name: Oryn, scheme: oryn)
├── babel.config.js
├── package.json
└── src/
    ├── constants/theme.js           ← Colors, categories, typography
    ├── context/AppContext.js        ← Global state, storage, live FX rates
    ├── hooks/useTheme.js
    ├── utils/helpers.js             ← Filters, CSV export, date helpers
    ├── components/
    │   ├── UI.js                    ← Shared components
    │   └── TransactionRow.js
    └── screens/
        ├── SetupScreen.js           ← Oryn onboarding (5 steps)
        ├── HomeScreen.js            ← Dashboard
        ├── AddTransactionScreen.js  ← Add / Edit flow
        ├── TransactionDetailScreen.js
        ├── TransactionsScreen.js    ← Full history
        ├── InsightsScreen.js        ← Charts & analytics
        ├── BudgetsScreen.js         ← Budget management
        └── SettingsScreen.js        ← Oryn settings + export
```
