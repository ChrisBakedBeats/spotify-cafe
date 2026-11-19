# House Café · Spotify edition

A single static page (`index.html`) that logs into Spotify (Authorization Code + PKCE, no server or client secret needed), plays through the Spotify Web Playback SDK, and shows your now-playing track and album art on the café's turntable.

## Setup

1. **Create a Spotify app** at https://developer.spotify.com/dashboard
   - Tick **Web Playback SDK** (and Web API) under "APIs used".
   - Add a **Redirect URI** that matches exactly where you will open the page, for example `http://127.0.0.1:8000/`
     - Spotify only allows `https://` URLs or loopback IPs such as `127.0.0.1`. `localhost` is not accepted.
     - Include the trailing slash and port.
2. **Copy the Client ID** and paste it into `CLIENT_ID` at the top of the script in `index.html`.
3. **Add yourself** under the app's *User Management* (development-mode apps only work for listed users).
4. **Serve the folder** (don't open the file directly):
   ```
   cd house-cafe-spotify
   python3 -m http.server 8000 --bind 127.0.0.1
   ```
   then open `http://127.0.0.1:8000/`. To host it online, put the folder on any HTTPS static host (Netlify, Vercel, GitHub Pages) and register that URL as the Redirect URI instead.
5. Click **Connect Spotify**, approve, then **Open the café**. Pick a playlist or paste any Spotify playlist, album, or track link.

## Good to know

- **Spotify Premium is required** (for the Web Playback SDK and for development-mode apps).
- Development-mode apps are limited to **5 authorized users** and one Client ID per developer.
- **The lights follow a beat clock, not the audio.** Spotify's browser audio is DRM-protected and its tempo/analysis endpoints are closed to new apps, so the page can't hear the music. Use **Tap** to tap along with the kick and sync the pulses and BPM.
- The login and tokens are stored in your browser's localStorage only.
- "Just look around" runs the scene without Spotify.

## Deploy with GitHub Actions (GitHub Pages)

`.github/workflows/deploy.yml` publishes the page to GitHub Pages on every push to `main`. The Client ID stays out of the repo: the workflow swaps it into the page at build time. (A PKCE Client ID isn't a secret, so a repository *variable* is fine.)

1. Push this folder's contents to the **root** of a GitHub repo (`index.html` and `.github/` at the top level), on the `main` branch.
2. In the repo: **Settings → Pages → Source: GitHub Actions**.
3. **Settings → Secrets and variables → Actions → Variables → New repository variable**: name `SPOTIFY_CLIENT_ID`, value is your Client ID.
4. In your Spotify app, add the Pages address as a **Redirect URI**, exactly: `https://<your-username>.github.io/<repo-name>/` (trailing slash included). Keep `http://127.0.0.1:8000/` too if you still want local runs.
5. Push (or run the workflow from the **Actions** tab). The deployed URL appears in the workflow summary.

If the build fails with "Add a repository variable", step 3 is missing. If Spotify says *INVALID_CLIENT: Invalid redirect URI*, the address in step 4 doesn't match the page's URL character for character.
