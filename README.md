# Ocjene — Grade Tracker

A small, offline Android app for tracking school grades, calculating averages and
finding out **which grades you still need** to reach a target average.
Built as a single-page web app (HTML/CSS/JS) and packaged for Android with
[Capacitor](https://capacitorjs.com/).

> **Bosanski (kratko):** Ocjene je aplikacija za praćenje ocjena po predmetima i
> polugodištima, s izračunom prosjeka i potrebnih ocjena za željeni cilj.
> Radi bez interneta, podaci ostaju na telefonu. Aplikacija ima bosanski i engleski jezik.

## Screenshots

<!-- Add 2-3 phone screenshots to docs/ and uncomment:
<p>
  <img src="docs/screenshot-main.png" width="240">
  <img src="docs/screenshot-goal.png" width="240">
  <img src="docs/screenshot-settings.png" width="240">
</p>
-->

## Features

- Multiple **profiles/users**, each with its own subjects, grades and colour theme
- **Two semesters** per profile; the app reopens where you left it
- Per-subject average and an **overall average** with a verbal grade
  (Excellent, Very good, Good, Sufficient, Insufficient)
- **Goal calculator** per subject: the next grade you need, or how many fives in a row
- **Overall goal calculator**: which subjects to improve first to reach your target
- 8 colour themes
- **Bosnian and English** interface (switch in Settings)
- Haptic feedback (can be turned off), Android back button support, themed status bar
- **Backup**: export all data as text and import it again
- Works fully **offline**; nothing is sent anywhere

## Install (Android)

1. Open the [latest release](../../releases/latest) on this page.
2. Download the `.apk` file and open it on your phone.
3. If Android asks, allow **"Install unknown apps"** for your browser or file manager.
   This is normal for apps that are not installed from Google Play.

## How the calculations work

- **Average** of a subject = sum of grades / number of grades.
  The overall average is the mean of the subject averages (subjects with at least one grade).
- **Single-subject goal** (target `T`, `n` grades, sum `s`):
  the next grade must be at least `ceil(T·(n+1) − s)`.
  If that is more than 5, the app computes how many fives `k = ceil((T·n − s) / (5 − T))` are needed in a row.
  A target of 5.00 is impossible once any grade is below 5.
- **Overall goal**: a greedy algorithm repeatedly adds a five to the subject where it raises
  that subject's average the most (lowest average, fewest grades), until the overall target
  is reached. It stops after 80 fives and reports that the goal is too far away.

## Tech stack

- HTML, CSS and vanilla JavaScript (no framework, no build step)
- [Capacitor](https://capacitorjs.com/) for the Android wrapper
- Plugins: `@capacitor/preferences` (storage), `@capacitor/haptics`,
  `@capacitor/app` (back button), `@capacitor/status-bar`
- GitHub Actions to build and sign the release APK

## Data and privacy

All data is stored on the device (Capacitor Preferences, mirrored to `localStorage`).
The app has no accounts, no analytics and makes no network requests.
Uninstalling the app deletes its data, so use **Settings → Export** to keep a backup.

## Build from source

Requirements: Node.js 22+, JDK 21, Android Studio.

```bash
git clone https://github.com/LolzerOG/Ocjene-App--Grades-App.git
cd Ocjene-App--Grades-App
npm ci
npx cap sync android
npx cap open android     # then Run in Android Studio
```

The whole app lives in [`www/index.html`](www/index.html). After editing it, run
`npx cap sync android` again.

## Releasing a new version

The workflow in `.github/workflows/release.yml` builds and signs the APK.

One-time setup: add these **repository secrets** (Settings → Secrets and variables → Actions):

| Secret              | Value                                              |
| ------------------- | -------------------------------------------------- |
| `KEYSTORE_BASE64`   | your `.jks` keystore, base64-encoded               |
| `KEYSTORE_PASSWORD` | keystore password                                  |
| `KEY_ALIAS`         | key alias used when the keystore was created       |
| `KEY_PASSWORD`      | key password                                       |

Then, for every release:

```bash
git tag v1.0.0
git push origin v1.0.0
```

The APK appears under **Releases** a few minutes later. The same keystore must be used for every
version, otherwise Android will not install an update over the old app.

## Project structure

```
www/index.html            the whole app (UI, logic, styles, translations)
android/                  Capacitor Android project
capacitor.config.*        app id (com.fatih.ocjene) and web directory
.github/workflows/        APK build and release workflow
```

## AI assistance

*(Edit or remove this section according to your course rules.)*
Parts of this app were developed with the help of an AI assistant (Claude by Anthropic).
The code was reviewed and tested by the author.

## License

[MIT](LICENSE)
