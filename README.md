# LOCK-IN App — Setup & Deployment
**By Adnan Sami**


## FEATURES

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
