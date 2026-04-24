# LOCK-IN App — Setup & Deployment Guide
**By Adnan Sami**

---

## STEP 1 — Firebase Project Setup

### 1.1 Create Project
1. Go to https://console.firebase.google.com
2. Click **Add Project**
3. Name it: `lockin-app`
4. Disable Google Analytics (not needed)
5. Click **Create Project**

### 1.2 Enable Authentication
1. In Firebase console → **Build → Authentication**
2. Click **Get Started**
3. Under **Sign-in method** → Enable **Email/Password**
4. Save

### 1.3 Create Firestore Database
1. In Firebase console → **Build → Firestore Database**
2. Click **Create Database**
3. Choose **Production mode**
4. Select region closest to Bangladesh: `asia-south1`
5. Click **Done**

### 1.4 Set Firestore Security Rules
Go to **Firestore → Rules** and paste this:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }

    match /dailyLogs/{logId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.uid;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.uid;
    }

    match /transactions/{txId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.uid;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.uid;
    }
  }
}
```

Click **Publish**

### 1.5 Create Firestore Indexes
Go to **Firestore → Indexes** and add these **Composite Indexes**:

**Index 1 — Daily Logs by uid + date:**
- Collection: `dailyLogs`
- Fields: `uid (Ascending)`, `date (Ascending)`

**Index 2 — Daily Logs by uid + date range:**
- Collection: `dailyLogs`
- Fields: `uid (Ascending)`, `date (Ascending)`

**Index 3 — Transactions by uid + createdAt:**
- Collection: `transactions`
- Fields: `uid (Ascending)`, `createdAt (Descending)`

Firebase will prompt you to create these automatically the first time queries run — just click the link in the browser console error.

### 1.6 Get Your Firebase Config
1. In Firebase console → **Project Settings** (gear icon)
2. Scroll to **Your apps** → Click **Web** icon `</>`
3. Register app name: `lockin-web`
4. Copy the `firebaseConfig` object

---

## STEP 2 — Add Firebase Config to the App

Open `lockin-app.html` and find this section near the top:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Replace with your actual config. Example:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyABC123...",
  authDomain: "lockin-app-xyz.firebaseapp.com",
  projectId: "lockin-app-xyz",
  storageBucket: "lockin-app-xyz.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## STEP 3 — Test Locally

Simply open `lockin-app.html` in Chrome or Edge.

**Note:** Because the app uses Firebase modules (type="module"), it must be served over HTTP, not just opened as a file. Use one of:

```bash
# Option A — Python (if installed)
python -m http.server 8080
# Then open: http://localhost:8080/lockin-app.html

# Option B — VS Code
Install "Live Server" extension → Right-click file → "Open with Live Server"

# Option C — Node.js
npx serve .
# Then open the URL shown
```

---

## STEP 4 — Deploy to Netlify (Recommended)

### 4.1 Drag & Drop Deploy (Easiest)
1. Go to https://netlify.com → Log in
2. Click **Add new site → Deploy manually**
3. Drag your `lockin-app.html` file into the deploy area
4. Netlify gives you a live URL like `https://amazing-locks-123.netlify.app`

### 4.2 Custom Domain (Optional)
1. In Netlify → **Domain settings**
2. Add custom domain: `lockin.yourdomain.com`
3. Update DNS as instructed

### Deploy to Vercel (Alternative)
1. Go to https://vercel.com
2. Click **Add New → Project**
3. Upload or connect GitHub repo
4. Deploy

---

## STEP 5 — Add to Home Screen (PWA Feel)

Tell users to:
1. Open the site in Chrome on their phone
2. Tap the 3-dot menu → **Add to Home Screen**
3. The app opens full-screen like a native app

To make it a real PWA, add this `<head>` tag:
```html
<meta name="theme-color" content="#080808"/>
<link rel="manifest" href="/manifest.json"/>
```

And create `manifest.json`:
```json
{
  "name": "LOCK-IN",
  "short_name": "LOCK-IN",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#080808",
  "theme_color": "#00ff87",
  "icons": [
    { "src": "/icon-192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "/icon-512.png", "sizes": "512x512", "type": "image/png" }
  ]
}
```

---

## DATA STRUCTURE REFERENCE

### Collection: `users`
```
users/{uid}
  uid: string
  email: string
  createdAt: ISO string
  streak: number
  bestStreak: number
  strictMode: boolean
```

### Collection: `dailyLogs`
```
dailyLogs/{auto-id}
  uid: string
  date: "YYYY-MM-DD"
  gym: boolean
  business: boolean
  money: boolean
  score: 0 | 1 | 2 | 3
```

### Collection: `transactions`
```
transactions/{auto-id}
  uid: string
  amount: number
  type: "income" | "expense"
  note: string
  createdAt: ISO string
```

---

## FEATURES INCLUDED

| Feature | Status |
|---------|--------|
| Email/Password Auth | ✅ |
| Today's Task Dashboard | ✅ |
| Gym / Business / Money toggle | ✅ |
| Live Score (0–3) | ✅ |
| Status badge (LOCKED IN / AVERAGE / SLIPPING / FAILED) | ✅ |
| Streak tracking | ✅ |
| Best streak | ✅ |
| Weekly bar chart | ✅ |
| Per-category completion stats | ✅ |
| Money tracker (income/expense) | ✅ |
| Balance display | ✅ |
| Weekly money summary | ✅ |
| Strict Mode toggle | ✅ |
| Strict Mode warning screen | ✅ |
| Sign out | ✅ |
| Dark mode only | ✅ |
| Mobile-first responsive | ✅ |
| Glassmorphism UI | ✅ |

---

## TROUBLESHOOTING

**"Permission denied" error in Firestore**
→ Check your Firestore Security Rules. Make sure they're published.

**App shows blank page**
→ Must be served over HTTP. Don't open as a raw file. Use Live Server or `python -m http.server`.

**Queries not working**
→ Create the Firestore Indexes (Step 1.5). Firebase prints the link in console automatically.

**Transactions not loading in order**
→ The `createdAt DESC` index is required. Create it in Firestore → Indexes.

---

Built by Adnan Sami · LOCK-IN v1.0
