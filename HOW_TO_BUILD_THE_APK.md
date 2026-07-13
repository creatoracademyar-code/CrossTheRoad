# Cross the Road — Android Project

This folder is a complete Android Studio project that wraps your exact game
(same HTML/CSS/JS, gameplay untouched) in a full-screen native app. High
score is saved automatically via the browser storage the game already uses
— it survives closing/reopening the app, and is wiped only if the app is
uninstalled.

I can't generate the compiled `.apk` file myself (that requires Google's
Android SDK build tools and internet access, which aren't available in this
chat environment). But turning this project into a real APK on your own
computer takes about 2 minutes and needs **no coding** — just clicking
buttons in a free tool. Steps below.

## What you need
- A Windows/Mac/Linux computer
- **Android Studio** (free): https://developer.android.com/studio

## Steps

1. Install Android Studio if you don't have it, and open it.
2. Choose **Open** (not "New Project") and select this folder
   (`CrossTheRoadApp`).
3. Wait for Gradle to sync (it will download the Android build tools the
   first time — this needs internet, just once).
4. Once synced, go to the top menu: **Build → Build Bundle(s) / APK(s) →
   Build APK(s)**.
5. When it finishes, click the **"locate"** link in the notification, or
   find the file at:
   `app/build/outputs/apk/release/app-release-unsigned.apk` (or under
   `debug/` if you built a debug APK instead).
6. Copy that `.apk` file to your phone (email it to yourself, USB, Google
   Drive, etc.) and tap it to install. You'll need to allow "install from
   unknown sources" the first time — that's a standard Android prompt.

## To publish on the Play Store
The Play Store requires a **signed** app bundle (`.aab`), not a raw APK:
1. In Android Studio: **Build → Generate Signed Bundle / APK**.
2. Choose **Android App Bundle**, then **Create new key store** (this is
   your personal signing key — keep the file and password safe forever;
   you need the same one for every future update).
3. Follow the wizard, choose **release**, and it produces a signed
   `.aab` file.
4. Create a developer account at https://play.google.com/console
   (one-time $25 fee), create a new app listing, and upload that `.aab`.

## Project contents
- `app/src/main/assets/index.html` — your exact game, unmodified gameplay.
- `app/src/main/java/.../MainActivity.java` — a single-screen native
  wrapper: a full-screen WebView that loads the game and enables local
  storage so the high score save/load code (already in the game) works.
- Standard Gradle/manifest/icon files needed for any Android app.

## Changing the app name / package / icon
- App name: edit `app/src/main/res/values/strings.xml`.
- Package ID (`com.anthropic.crosstheroad`): change it in
  `app/build.gradle` (`applicationId`) and in the Java file's package
  declaration + folder path if you want a different one for the Play
  Store listing.
- Icon: replace the PNGs in `app/src/main/res/mipmap-*/` (a placeholder
  icon is included so the project builds out of the box).
