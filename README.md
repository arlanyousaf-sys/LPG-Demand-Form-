# LPG Demand Manager — Tehzeeb Bakers

A mobile-first Progressive Web App (PWA) for creating and tracking daily **LPG cylinder demand slips**. Demands, history and settings are stored in **Firebase** so every device using the same login sees the same data.

**Live app:** https://arlanyousaf-sys.github.io/LPG-Demand-Form-/

## Features

- **Login / Signup** with Firebase Authentication (username or email + password). Several people can use one shared login and see the same history.
- **New Demand** — branch, empty cylinders, replace cylinders, delivery time, date and remarks (max 90 characters).
  - *Demand Cylinder* defaults to **Empty + Replace**, and can be edited manually.
  - Share as WhatsApp text, picture preview, or PDF.
- **History** — every demand as a slip.
  - Edit *Empty*, *Demand Cylinder* and *Received* directly inside the slip.
  - *Received Date* shows `-` until cylinders are received (or a date is selected).
  - PDF / WhatsApp picture show the received lines only after receiving.
- **Pending notifications (🔔)** — after 10:00 AM, demands not yet received are listed. Mark them received from the list; if it is late (3rd day or more) an inline date picker asks for the received date.
- **Mobile notifications** via the service worker (see *Notes*).
- **Monthly report** — PDF, WhatsApp picture, and Gmail (PDF attachment). The Remarks column is left blank for handwriting.
- **Settings** — logo, banner, signatures and names, theme, backup / restore (.json).
- **Cloud sync toasts** — a message appears when data is saved to the cloud or new data arrives from another device.
- **Offline support** — works offline and syncs when the internet returns.
- **Installable** on Android via Chrome (Install app / Add to Home screen).

## Tech stack

- Single-file HTML / CSS / JavaScript app (`index.html`)
- Firebase Authentication + Cloud Firestore (v10 compat SDK)
- Service worker (`sw.js`) + Web App Manifest (`manifest.json`)
- html2canvas and jsPDF for pictures and PDF files
- Hosted on GitHub Pages

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app |
| `sw.js` | Offline cache and notification click handling |
| `manifest.json` | PWA name, colours and icons |
| `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` | App icons |

## Setup

1. **Create a Firebase project** at https://console.firebase.google.com and register a **Web app**.
2. Paste the web config into `FIREBASE_CONFIG` near the top of the script in `index.html`.
3. **Authentication → Sign-in method:** enable **Email/Password**.
4. **Firestore Database:** create a database (production mode).
5. **Firestore → Rules:** publish these rules so each login can only access its own data:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

6. Upload all files to a GitHub repository (same folder, no sub-folders) and enable **Settings → Pages** (branch `main`, folder `/ (root)`).
7. **Authentication → Settings → Authorized domains:** add `your-username.github.io`.

## Data structure

```
users/{uid}/records/{recordId}   → demand slips
users/{uid}/settings/{key}       → logo, banner, signatures, names
```

## Notes

- **Firebase API key:** the web API key in `index.html` is public by design. Data is protected by the Firestore rules and by login, not by hiding the key. GitHub may show a "secret scanning" alert for it; this is a false positive.
- **Password reset:** Firebase sends a reset link only to real email addresses. Accounts created with a plain username must be reset from the Firebase Console.
- **Notifications:** they appear when the app is open or running in the background. Android does not deliver them after the app is fully closed. Allow notifications for Chrome / the site in phone settings.
- **Updating the app:** after uploading new files, close and reopen the app once so the new service worker takes over. Reinstall the app to see a new home-screen icon.
- Large logo / signature images (more than about 700 KB) may fail to save to the cloud; use smaller images.

## Credits

Built for Tehzeeb Bakers — LPG Cylinder Demand Manager.
