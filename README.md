# My Day Quest ☀️

A fun daily activity logger and planner for kids, with optional real-time
cloud sync so every family device sees the same profiles and stars.

## Deploy to Vercel

1. Go to https://vercel.com and sign in
2. Go to https://vercel.com/new -> "Deploy without Git" -> drag this whole folder in
3. Click Deploy

By default the app works exactly as before, saving locally on each device.
To make every device share the same data, follow the cloud sync setup below.

## Turn on cloud sync (free, ~10 minutes, one-time)

This uses Firebase (a free Google service) as the shared "notebook" all your
devices read from and write to.

### 1. Create a free Firebase project
- Go to https://console.firebase.google.com
- Click **Add project**, name it anything (e.g. "my-day-quest"), and finish the wizard
  (you can turn off Google Analytics, it isn't needed)

### 2. Create a Firestore database
- In the left sidebar, click **Build -> Firestore Database**
- Click **Create database**
- Choose **Start in test mode** (we'll tighten the rules in a moment)
- Pick any location close to you, click **Enable**

### 3. Set security rules
- Still in Firestore, click the **Rules** tab
- Replace the contents with:
  ```
  rules_version = '2';
  service cloud.firestore {
    match /databases/{database}/documents {
      match /families/{familyCode} {
        allow read, write: if true;
      }
    }
  }
  ```
- Click **Publish**
- (This keeps the family's data open only to whoever knows your unique
  FAMILY_CODE below — not truly private, but effectively so, same idea as
  the unguessable Vercel link. Combined with the app's own PIN lock, this
  is a reasonable level of protection for a family app.)

### 4. Get your config values
- Click the gear icon (top left) -> **Project settings**
- Scroll to **Your apps**, click the **</>** (web) icon to register a new app
- Give it any nickname, skip Firebase Hosting, click **Register app**
- You'll see a code block with a `firebaseConfig` object — you need these values

### 5. Paste your config into the app
- Open `src/firebaseConfig.js` in this folder with any text editor
- Replace each `PASTE_..._HERE` with your real value from step 4
- Set `FAMILY_CODE` to any word only your family will use, e.g. `"morrison-quest-7788"`
- Save the file

### 6. Redeploy
- Go back to Vercel, upload this folder again (same steps as before) —
  or if you connected GitHub, just push the change
- Open the app on each device — they'll now all show the same profiles,
  syncing within a second or two of any change

## How data storage works

- **Without cloud sync set up:** each device/browser keeps its own separate
  data (same as the local HTML version)
- **With cloud sync set up:** all devices share one live copy, stored in
  your free Firestore database, and update in real time
- The app always also keeps a local backup copy on each device, so it still
  works offline and syncs back up once reconnected

## Put it on the kids' devices
- **Tablet/phone**: open the URL -> Share -> Add to Home Screen
- **Windows/Mac laptop (Chrome/Edge)**: open the URL -> click the install icon
  in the address bar
- **Mac (Safari)**: File -> Add to Dock

## Customizing
- **Activities**: edit `LIBRARY` in `src/App.jsx`
- **Sub-categories**: edit the `subs` array on any LIBRARY item
- **Badges**: edit `BADGES`
- **Avatars**: edit `AVATARS`
