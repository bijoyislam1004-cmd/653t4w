# Jankhali Premier League Auction (React + Tailwind + Firebase)

## Structure
index.html, vite.config.js, package.json, .env.example, firestore.rules, src/{main.jsx,index.css,firebase.js,App.jsx}

## Firebase setup
1. console.firebase.google.com > Add project.
2. Build > Authentication > Sign-in method > enable Email/Password. Add 5 users (admin + 4 teams).
3. Build > Firestore Database > Create database. Then Rules tab: paste firestore.rules and Publish.
4. Project settings > Your apps > Web (</>) > copy the config. Copy .env.example to .env and fill the 4 values.
5. Roles: in Firestore create collection `users`; one document per account, document ID = that user UID (Authentication tab):
   - admin: `{ role: "admin" }`
   - teams: `{ role: "team", teamId: "t1" }` ... `t2`, `t3`, `t4`
   (The first time admin logs in, the app creates teams t1-t4, settings and auction state automatically.)

## Run locally
npm install && npm run dev

## Deploy
npm run build, then either `firebase init hosting` (public dir `dist`, single-page app yes) and `firebase deploy`, or upload `dist/` to Netlify/Vercel.

## Database
- settings/main: leagueName, budget, inc, categories[], rules
- teams/{t1..t4}: name, logo(base64), budget, spent
- players/{id}: name, photo(base64), category, base, status(pending|sold|unsold), teamId, price, soldAt
- auction/state: status(idle|live|paused|ended), playerId, bid, bidder, bidding, history[{team,amount}]
- history/{id}: name, category, base, price, teamId, status, at
- users/{uid}: role, teamId

## Safety notes
Bid and sold logic run in Firestore transactions (budget check, no double sale). Rules stop teams from writing anything except their own bid. A team could still bypass the budget check with a custom client; for hard enforcement move bid() into a Cloud Function.
Images are resized to about 320px and stored in the document (no Storage billing needed).
