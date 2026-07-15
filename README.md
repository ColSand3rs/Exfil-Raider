# Exfil Raiders

A 2D run-and-gun platformer inspired by Escape from Tarkov — Mario-shaped, but
you're shooting Scavs and rival PMCs instead of stomping Goombas. Six raids,
each with patrolling enemies, ammo/medkit pickups, sealed loot cases, and an
extraction point. Extract successfully and your loot, weapon unlocks, armor
tier, and task progress carry over to a persistent stash between sessions.

**Features**
- 6 raids (Customs Yard, Pine Woods, Dorms, The Factory, Shoreline, Lighthouse),
  increasing in difficulty, two with Boss Scav fights
- Scavs and rival PMCs as enemies, plus a Boss on two of the raids
- A stash hub with three panels: **Inventory** (equip unlocked weapons, view
  armor tier, open sealed cases for random rewards), **Tasks** (standing
  objectives like "kill 5 PMCs" for bonus rewards), and raid selection
- Progress (roubles, unlocked weapons, armor tier, task progress, unlocked
  raids) is saved to local storage and persists between sessions
- Die in a raid and you lose that raid's loot — but not your stash

Built with plain HTML5 Canvas + JavaScript (no frameworks, single file),
wrapped into an Android app with [Capacitor](https://capacitorjs.com/).

**Controls**
- Move: Arrow keys / A,D
- Jump: Space / Up / W / on-screen ▲
- Shoot: J or X or mouse click (desktop) — on-screen scope button on mobile

---

## 1. Put this on GitHub

```bash
git init
git add .
git commit -m "Exfil Raiders"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

## 2. Get an APK — easiest way (no local Android setup needed)

This repo includes a GitHub Actions workflow at
`.github/workflows/android-build.yml` that automatically builds a debug APK
every time you push to `main`.

1. Push the repo to GitHub (step 1 above).
2. Go to your repo on GitHub → **Actions** tab.
3. You should see a workflow run called "Build Android APK" — wait for it to
   finish (a few minutes the first time).
4. Click into the finished run → scroll to **Artifacts** →
   download `exfil-raiders-debug-apk`.
5. Unzip it, you'll get `app-debug.apk`. Copy it to your phone (e.g. via
   Google Drive, USB, or email) and tap it to install (you'll need to allow
   "install from unknown sources" the first time).

That's it — no Android Studio required.

## 3. Play it in a browser first (optional sanity check)

Before worrying about Android at all, you can just open the game directly:

```bash
cd www
python3 -m http.server 8000
# then open http://localhost:8000 in your browser
```

Or open `www/index.html` directly in a browser.

## 4. Building locally instead (if you want Android Studio)

Requirements: Node.js 18+, Android Studio (for the SDK/emulator), JDK 17.

```bash
npm install
npx cap add android      # only needed once, creates the android/ folder
npx cap sync android
npx cap open android     # opens the project in Android Studio
```

From Android Studio, hit **Run** to install on an emulator or a plugged-in
phone, or **Build > Build Bundle(s)/APK(s) > Build APK(s)** to get an
installable file.

You can also build from the command line once `android/` exists:

```bash
cd android
./gradlew assembleDebug
# APK will be at android/app/build/outputs/apk/debug/app-debug.apk
```

## 5. Customizing

Everything — game logic, levels, weapons, enemies, the stash/inventory/tasks
hub, and rendering — lives in the single file `www/index.html`, inside one
`<script>` block. A few starting points if you want to tweak it:

- **Levels**: search for `var LEVELS = [` — each entry points at a `MAP*`
  ASCII map (search `var MAP1 =` etc.) plus a theme, time limit, and name/brief
  text. The map legend is documented in a comment just above the map data
  (`P` player, `S` scav, `F` PMC, `B` boss, `C` sealed case, `M` medkit,
  `A` ammo, `R` roubles, `E` exfil).
- **Weapons**: search for `var WEAPONS = {` to add/tune guns, and
  `var FACTIONS = {` to change starting kit per side.
- **Enemies**: search for `var ENEMY_KINDS = {` to tune scav/PMC/boss stats
  (health, range, fire rate, contact damage, colors).
- **Tasks**: search for `var TASK_DEFS = [` to add/edit standing objectives.
- **Look & feel / HUD / touch buttons**: the `<style>` block at the top of
  `www/index.html`.
- **App name / package id**: `capacitor.config.json`
  (change `appId` to something unique like `com.yourname.exfilraiders` before
  publishing — it must be unique if you ever publish to the Play Store).
- **App icons**: Capacitor uses default icons until you run
  `npx @capacitor/assets generate` with your own icon/splash images — optional,
  the app works fine without it for testing.

## Notes

- This is a debug build (unsigned), fine for installing on your own device
  for testing. If you want to publish to the Play Store, you'll need to
  generate a signing key and build a release APK/AAB — Android Studio's
  **Build > Generate Signed Bundle/APK** wizard handles this.
- No external assets or network calls — everything is drawn with canvas
  shapes, so there's nothing else to bundle.
- Progress is stored in the device's local storage (`localStorage`), tied to
  the app. Uninstalling the app clears it.
