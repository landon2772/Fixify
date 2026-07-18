# Fixify 🦫

**Repair, don't replace.** Fixify is a student repair club site: post a broken
item, a club "fixer" claims it, you chat until it's ready, then pick it up.

Live at **https://www.fixifyit.org** (GitHub Pages — every push to `main`
deploys automatically, usually within a minute).

## What's in this repo

| File | What it is |
|---|---|
| `index.html` | The whole app — landing page, sign-in, repair board, chat. Uses Firebase Auth + Firestore. |
| `firestore.rules` | Security rules for the Firestore database. **Not active until published — see below.** |
| `logo.png` / `favicon.png` / `apple-touch-icon.png` | The beaver. |
| `CNAME` | Tells GitHub Pages to serve the site at www.fixifyit.org. |

## ⚠️ Publish the security rules (one-time, important)

The admin/fixer checks inside `index.html` only shape what buttons people
see — anyone can open browser dev tools and bypass them. The rules in
`firestore.rules` are what actually stop a random student from deleting
listings, reading other people's chats, or making themselves an admin.

To publish them (needs access to the Firebase project `fixify-1`):

1. Open the [Firebase console](https://console.firebase.google.com/) →
   project **fixify-1** → **Firestore Database** → **Rules** tab.
2. Replace everything in the editor with the contents of `firestore.rules`.
3. Click **Publish**.
4. **Bootstrap step:** open www.fixifyit.org and sign in as the admin once,
   using **"Continue with Google"** (see the sign-in note below). The site
   then writes the fixer roster (`meta/fixers`) that the rules use to decide
   who may claim repairs. Until that happens, only the admin can claim.

That's it. The roster keeps itself up to date automatically whenever an
admin has the site open and the fixer list changes.

### ⚠️ Admins and fixers must sign in with Google (or a verified email)

The rules only grant admin/fixer powers to accounts with a **verified**
email address. This stops someone from registering an email+password account
that claims a fixer's or admin's address before that person signs up.

- **Continue with Google** always produces a verified email — this is the
  recommended path, and everyone on a `@menloschool.org` account already has
  Google.
- A plain email+password account is *not* verified, so it will silently lack
  claim/admin powers. (The app doesn't send verification emails yet, so
  Google sign-in is the reliable option for the crew.)

Regular submitters are unaffected — they can use email+password freely.

### What the rules enforce

- Only signed-in people can see the board, fixers, or send messages.
- You can only read chats you're a participant in.
- Only the item's owner, its claiming fixer, or an admin can update an item —
  and only admins can delete or delist listings, or edit the fixer crew.
- Only listed fixers (and admins) can claim repairs.

### Keeping things in sync

- **Admins:** the email list lives in **two places** — `ADMIN_EMAILS` in
  `index.html` and `isAdmin()` in `firestore.rules`. If you add an admin,
  update both.
- **Fixers:** add fixers on the site (Fixers tab → "Add a fixer") using the
  **email they actually sign in with** — that email is what unlocks claiming.

## Working on the site locally

No build step. From the repo folder run:

```
python3 -m http.server 8766
```

then open http://localhost:8766. Sign-in against the real Firebase project
works from localhost.
