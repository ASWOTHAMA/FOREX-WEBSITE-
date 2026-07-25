# Aurum Forex Academy — admin modules

Adds the admin panel, course/content management, payments, and
certificates to the existing project.

## Files in this delivery

```
admin.html            analytics dashboard
students.html         student list, search, edit, attendance, delete
courses.html          curriculum (lesson) CRUD
videos.html           video upload to Storage + playback
notes.html             PDF upload to Storage + viewer
certificates.html     real PDF certificate generation (jsPDF)
announcements.html    post/list/delete announcements
payments.html         payment history, manual logging, Razorpay checkout wiring

js/admin.js            requireAdmin() guard + admin.html analytics
js/students.js js/courses.js js/videos.js js/notes.js
js/certificates.js js/announcements.js js/payments.js

css/admin.css          tables, modals, badges, upload UI

firestore.rules        updated rules — replace what you set up earlier
storage.rules           updated rules — replace what you set up earlier
functions/index.js     REFERENCE Razorpay Cloud Functions — NOT deployed
                        or tested by Claude, see warning below
```

## Setup steps, in order

**1. Bootstrap your first admin.** Nothing in this app can grant admin —
that's intentional (see security note below). Go to Firebase Console →
Firestore → `users` → find your own account's document → manually add
a field `role` (string) = `admin`. After that, you can promote/demote
other admins... actually you can't yet — this project has no "make
another user admin" UI. Do it the same manual way in the Console, or
ask me to add that UI to `students.html` next.

**2. Replace your Firestore and Storage rules** with the versions in
this delivery — they supersede the ones from the auth-only batch
(those didn't know about roles, courses, videos, etc.).

**3. Razorpay is NOT ready to accept real payments yet.** The checkout
button on `payments.html` will tell you it's "not configured" until you:
   - Deploy `functions/index.js` (see the setup comment inside that file)
   - Paste the two resulting Cloud Function URLs and your Razorpay
     **public** Key ID into the fields on `payments.html`
   Until then, use the manual payment logging form — it's fully
   functional today and is how many small course businesses actually
   verify UPI payments in practice.

## Why some things are scoped the way they are

- **"Delete student" only deletes their Firestore profile**, not their
  Firebase Auth login. Deleting an auth account requires the Admin SDK
  (server-side). Use "Suspend" for real access revocation — suspending
  sets `suspended: true`, which you'd check in your rules or app logic
  to block a student's actions. (Currently `suspended` is stored and
  shown in the UI; it isn't yet enforced anywhere to actually block a
  suspended student from logging in or using the dashboard — that's a
  small addition to `js/auth.js` / `js/dashboard.js` if you want it.)
- **Cloud Functions code is reference only.** I don't have a way to
  deploy or run Firebase Cloud Functions from this chat environment,
  so I can't verify it works end-to-end the way I could verify the
  Firestore/Storage code (which runs directly in your browser and
  follows Firebase's current client SDK exactly). Test it in a Razorpay
  test-mode account before going live.
- **Announcements don't show on the student dashboard yet** — you asked
  me not to regenerate existing files, and displaying them is a change
  to `dashboard.js`. Say the word and I'll add it.
- **No "promote to admin" UI** — deliberately, since building that UI
  safely means also making sure a non-admin can never call it, which
  is exactly the kind of thing worth testing carefully rather than
  shipping quickly.
