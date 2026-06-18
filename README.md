# Jesse's Diary

A personal web diary that saves entries and photos to your own Google Drive. Built as a single HTML file with no server — just open it in a browser, sign in with Google, and start writing.

---

## How your data is stored

Everything lives in **your own Google Drive** — specifically in a hidden "app data" folder that only this diary can see. There is no server, no database, and no third party that can read your entries. The data is:

- **`entries.json`** — one file containing all diary text
- **`{id}_{n}.jpg` / `.mp4` / etc.** — each uploaded photo or video as a separate file

When you sign out or revoke access, the diary can no longer read the data, but the files remain safely in your Drive. You can find them via **Google Drive → Storage → Hidden app data**.

---

## One-time setup

### Step 1 — Create a Google Cloud project

1. Go to [console.cloud.google.com](https://console.cloud.google.com) and sign in with your Google account.
2. Click the project dropdown at the top → **New Project**.
3. Give it any name (e.g. "Jesse's Diary") and click **Create**.
4. Make sure your new project is selected in the dropdown.

### Step 2 — Enable the Google Drive API

1. In the left sidebar go to **APIs & Services → Library**.
2. Search for **Google Drive API** and click it.
3. Click **Enable**.

### Step 3 — Configure the OAuth consent screen

1. Go to **APIs & Services → OAuth consent screen**.
2. Choose **External** and click **Create**.
3. Fill in:
   - **App name**: Jesse's Diary
   - **User support email**: your email address
   - **Developer contact email**: your email address
4. Click **Save and Continue** through the Scopes screen (you don't need to add any manually).
5. On the **Test users** screen, click **Add users** and add your own Gmail address. This is important — while the app is in "testing" mode, only listed users can sign in.
6. Click **Save and Continue**, then **Back to Dashboard**.

> **About the "unverified app" warning:** Because this is a personal app that hasn't been reviewed by Google, you'll see a screen saying "Google hasn't verified this app" the first time you sign in. This is completely normal and expected for private/personal apps. Click **Advanced → Go to Jesse's Diary (unsafe)** to proceed. It's safe — you own both the app and the Google account.

### Step 4 — Create OAuth credentials

1. Go to **APIs & Services → Credentials**.
2. Click **Create Credentials → OAuth client ID**.
3. Choose **Web application**.
4. Set the **Name** to anything (e.g. "Diary Web Client").
5. Under **Authorized JavaScript origins**, add your GitHub Pages URL:
   ```
   https://YOUR_GITHUB_USERNAME.github.io
   ```
   (For local testing, also add `http://localhost` and `http://127.0.0.1`)
6. Leave **Authorized redirect URIs** empty — this app uses the implicit token flow, which doesn't need a redirect URI.
7. Click **Create**.
8. Copy the **Client ID** — it looks like `123456789-abc...xyz.apps.googleusercontent.com`.

### Step 5 — Paste your Client ID into the diary

Open `diary.html` in a text editor and find this line near the top of the `<script>` section:

```js
const CLIENT_ID = 'YOUR_GOOGLE_CLIENT_ID.apps.googleusercontent.com';
```

Replace the placeholder with your actual Client ID:

```js
const CLIENT_ID = '123456789-abc...xyz.apps.googleusercontent.com';
```

Save the file. That's the only code change needed.

---

## Deploying to GitHub Pages

### Step 1 — Create a GitHub repository

1. Go to [github.com](https://github.com) and create a new repository.
2. You can name it anything (e.g. `jesses-diary`).
3. **Important:** GitHub Pages is free on public repos. The repo being public is fine — the diary's actual privacy comes from Google sign-in. Anyone who visits the URL will see a "Sign in with Google" screen, and only your listed test users can access the data. The code itself is not sensitive.

### Step 2 — Push the file

```bash
git init
git add diary.html
git add imgs/          # if you have an icon
git commit -m "Initial diary"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

### Step 3 — Enable GitHub Pages

1. In your repository on GitHub, go to **Settings → Pages**.
2. Under **Source**, select **Deploy from a branch**.
3. Choose **main** branch and **/ (root)** folder.
4. Click **Save**.

Your diary will be live at `https://YOUR_USERNAME.github.io/YOUR_REPO/diary.html` within a minute or two.

> **After you get your Pages URL:** Go back to the Google Cloud Console → Credentials → your OAuth client → edit it and add the full Pages URL to **Authorized JavaScript origins**, e.g. `https://yourname.github.io`. Without this, sign-in will fail with an "origin not allowed" error.

---

## Known limitations

- **"Unverified app" screen:** Expected for personal apps. Click through it once and your browser will remember.
- **Sign-in required on every new browser/device:** Access tokens last one hour and are not persisted across page reloads. You'll be asked to sign in again after closing the tab, but it should be a single click (no password re-entry) if you're already signed into Google in that browser.
- **Mobile browsers:** Works in mobile Chrome and Safari. The Google sign-in popup may briefly appear and close — this is normal.
- **Slow first load with many photos:** Photos are fetched from Google Drive when you open the app. Thumbnails load in the background; you'll see them appear as they arrive.
- **No offline support:** The diary requires an internet connection to load or save entries.
