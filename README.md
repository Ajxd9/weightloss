# Daily Progress

Offline food, weight and workout tracker. All data lives in the browser's
`localStorage` — no account, no server, no sync.

## Run it as a web app / PWA

Serve `www/` with any static file server and open `index.html`:

```
npx serve www
```

On Android Chrome (or iOS Safari), open the page and choose
**"Add to Home screen"**. It installs with its own icon, launches full-screen
(no browser chrome), and keeps working offline via the service worker in
`www/sw.js`. This works today with no native build step.

## Build the native Android APK

The `android/` folder is a ready-to-build Capacitor project wrapping `www/`
as a native WebView app (package id `com.dailyprogress.app`). It could not be
compiled inside this sandbox: the Android Gradle Plugin and platform SDK are
only distributed from `dl.google.com`, which this session's network policy
blocks (403). Build it on a machine with normal internet access and Android
Studio / the Android SDK installed:

```
npm install
npx cap sync android
cd android
./gradlew assembleDebug
```

The unsigned debug APK lands at
`android/app/build/outputs/apk/debug/app-debug.apk` — install it with
`adb install app-debug.apk` or copy it to a device.

To produce a signed release APK/AAB (e.g. for the Play Store), use Android
Studio's Build > Generate Signed Bundle/APK flow, or `./gradlew bundleRelease`
with a signing config added to `android/app/build.gradle`.

### After editing the web app

Any change to `www/` needs a re-sync before rebuilding:

```
npx cap sync android
```

## Project layout

- `www/` — the app itself (HTML/CSS/JS), plus `manifest.webmanifest`,
  `sw.js` (service worker) and `icons/` for PWA install support.
- `android/` — generated Capacitor Android project (native wrapper only;
  don't hand-edit generated files under `app/src/main/assets`).
- `capacitor.config.json` — app id, name and web asset directory.
