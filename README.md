# 📦 Asset Tracker

A lightweight web-based asset tracking system built with vanilla HTML/CSS/JS and Firebase Firestore. Designed for simple, fast asset management — scan a QR code, see where something is, book it out, mark its condition.

## Features

### Scanner (`index.html`)
- **QR code scanning** via device camera, or manual ID entry
- **Asset lookup** — instantly see location, description, and condition
- **Register new assets** by entering an unrecognised ID
- **Book out / return** — log who has an asset, where it's going, and any notes
- **Condition flags** — mark assets as ✅ Good, ⚠️ Needs Attention, or 🔴 Damaged
- **Book-out warning** — prompts a confirmation if you try to book out a flagged asset

### Admin Dashboard (`admin.html`)
- **Password protected** (SHA-256 hashed, optional "remember me")
- **Stats overview** — total assets, locations, off-site count, needs attention count
- **Filterable asset table** — search by ID/description, filter by location, status, or condition
- **Condition column** — colour-coded badges per asset
- **Inline return** — return off-site assets directly from the table
- **Remove assets** — permanently delete an asset record
- **Booking history tab** — full log of every checkout and return
- **Usage chart tab** — bar chart showing most-booked assets
- **CSV export** — export assets or booking history

## Tech Stack

- **Frontend** — Vanilla HTML, CSS, JavaScript (no framework)
- **Database** — [Firebase Firestore](https://firebase.google.com/docs/firestore) (NoSQL, real-time)
- **QR Scanning** — [html5-qrcode](https://github.com/mebjas/html5-qrcode)
- **Fonts** — [Inter](https://fonts.google.com/specimen/Inter) via Google Fonts
- **Hosting** — Firebase Hosting

## Firestore Data Structure

### `assets` collection
| Field | Type | Description |
|---|---|---|
| `id` | string | Asset ID (e.g. `000001`) |
| `location` | string | Current storage location |
| `description` | string | Human-readable description |
| `partNo` | string | Part number (optional) |
| `condition` | string | `good` \| `attention` \| `damaged` |
| `status` | string | `in` (on-site) \| `out` (off-site) |
| `currentBookingId` | string \| null | ID of active booking, if off-site |
| `updatedAt` | timestamp | Last modified |

### `bookings` collection
| Field | Type | Description |
|---|---|---|
| `assetId` | string | Reference to asset |
| `bookedBy` | string | Name of person who took it |
| `destination` | string | Where it's going / purpose |
| `notes` | string | Optional extra info |
| `bookedAt` | timestamp | When it was taken |
| `returnedAt` | timestamp \| null | When it was returned (`null` = still out) |

## Getting Started

1. Clone the repo
2. Set up a [Firebase project](https://console.firebase.google.com/) and enable Firestore
3. Replace the `firebaseConfig` object in both `index.html` and `admin.html` with your own project credentials
4. Deploy via Firebase Hosting:
   ```bash
   npm install -g firebase-tools
   firebase login
   firebase init
   firebase deploy
   ```

## Danger Folder

The `Danger folder/` contains utility pages for bulk operations:
- `reset-assets.html` — bulk reset asset statuses
- `cleanup-bookings.html` — clean up booking records

These are not linked from the main UI and should be used with caution.

## Admin Password

The admin password is hashed with SHA-256. To change it, generate a new hash:

```js
const hash = await crypto.subtle.digest('SHA-256', new TextEncoder().encode('your-new-password'));
console.log(Array.from(new Uint8Array(hash)).map(b => b.toString(16).padStart(2, '0')).join(''));
```

Replace the `ADMIN_HASH` constant in `admin.html` with the result.
