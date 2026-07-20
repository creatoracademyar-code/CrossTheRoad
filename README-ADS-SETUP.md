# Ads in Skip Street — now running on TEST ad IDs

This project is currently wired up with **Google's official test ad unit
IDs** for all four major AdMob formats, so you can confirm everything
works before your real ad units are approved:

- **App ID:** `ca-app-pub-3940256099942544~3347511713` (AndroidManifest.xml)
- **Banner:** `ca-app-pub-3940256099942544/6300978111`
- **Interstitial:** `ca-app-pub-3940256099942544/1033173712`
- **Rewarded:** `ca-app-pub-3940256099942544/5224354917`
- **App Open:** `ca-app-pub-3940256099942544/9257395921`

Test IDs always return a placeholder "Test Ad" regardless of your
AdMob account's review/approval status — safe to build and run right
now without any risk of invalid-traffic policy strikes.

## Where each ad appears & how it works

| Ad type | Where it shows | Trigger | What happens |
|---|---|---|---|
| **Banner** | Small strip pinned to the bottom of the screen | Loads automatically on app start, stays up the whole time | Doesn't interrupt gameplay; the game view just shrinks slightly to make room for it |
| **App Open** | Full screen, right when the app is launched | Cold start, before you see the game | Shown once per launch, then dismisses into the game. Skips itself silently if it isn't loaded in time |
| **Interstitial** | Full screen, between rounds | Every 3–5 deaths (randomized), *not* every death | Game pauses on the ad; resumes to the game-over screen once closed |
| **Rewarded** | Full screen video, opt-in | Player taps **"📺 Watch Ad to Continue"** on the game-over screen (offered once per run) | If watched fully: player revives in place with a 2s grace period. If closed early: no reward, stays on game-over screen with Restart |

## What's in the project
- `app/src/main/assets/index.html` — game calls
  `window.AndroidAds.showInterstitial()` every 3–5 deaths, and
  `window.AndroidAds.showRewarded()` when "Watch Ad to Continue" is tapped.
- `MainActivity.java` — loads/shows all 4 ad formats, wires the JS bridge
  both directions (game → native to request an ad, native → game via
  `evaluateJavascript` to report the ad closed / reward earned).
- `AndroidManifest.xml` — internet permission + App ID.
- `app/build.gradle` — AdMob library dependency (already present).

## Testing right now
1. Open the `SkipStreetApp` folder in Android Studio, let Gradle sync.
2. **Build → Build Bundle(s)/APK(s) → Build APK(s)**, install on your device.
3. You should see: an App Open test ad at launch, a banner at the bottom
   throughout, a test interstitial after 3–5 deaths, and a rewarded test ad
   when tapping "Watch Ad to Continue" on the game-over screen.
4. Every test ad is clearly labeled "Test Ad" in the corner.

## Before your real Play Store release
Swap all 4 test IDs back to your real AdMob IDs:
- In `AndroidManifest.xml`: your real App ID
  (`ca-app-pub-6758883482672554~1275508476`)
- In `MainActivity.java`: your real ad unit IDs for
  `BANNER_AD_UNIT_ID`, `INTERSTITIAL_AD_UNIT_ID`, `REWARDED_AD_UNIT_ID`,
  and `APP_OPEN_AD_UNIT_ID` (create the missing ones in the AdMob console
  if you haven't already — Apps → your app → Ad units → Add ad unit).

Then:
1. **Build → Generate Signed Bundle / APK** → **Android App Bundle** →
   create/use your signing key → build **release**. Keep the keystore
   file + password safe forever — you need the exact same one for every
   future update.
2. Publish your app publicly on Play Store (Production or Open testing
   track — must be public, not internal/closed, for AdMob to find it).
3. In AdMob, link the app to its Play Store listing, then wait for the
   app readiness review (Google checks app source/compliance; typically
   2–3 days) before real ads fully serve at full fill rate.

## Changing the ad frequency
In `app/src/main/assets/index.html`, inside the `Game` class, look at
`_randomAdThreshold()`:
```js
_randomAdThreshold() {
  return 3 + Math.floor(Math.random() * 3); // 3, 4, or 5
}
```
The first `3` is the minimum deaths between ads; the `3` inside
`Math.random() * 3` is the width of the range. For "every 4–6 deaths",
use `4 + Math.floor(Math.random() * 3)`.
