# OTP Reader

React Native (0.87) Android app that shows the latest Hotstar SMS and the OTP in it.

## Run

Needs Node 22+, JDK 17+, Android Studio (SDK) and a real Android phone with a SIM, USB debugging on.

    npm install
    npx react-native run-android

Allow the SMS permission when asked.

## Build an installable APK

    cd android && ./gradlew assembleRelease
    # android/app/build/outputs/apk/release/app-release.apk

The APK is signed with the debug key (`android/app/debug.keystore`). Switching to a different key later means everyone has to uninstall before updating.

## Sharing the APK (WhatsApp etc.)

Android blocks SMS access for apps installed from WhatsApp, a browser or a file manager. Each person has to do this once:

1. Play Store → profile picture → Play Protect → ⚙️ → turn off "Scan apps with Play Protect".
2. Install the APK.
3. Open OTP Reader → "Open settings" → ⋮ (top right) → "Allow restricted settings" → Permissions → SMS → Allow.
   If ⋮ is missing, try SMS → Allow once (it is refused), then go back and look again.
4. Return to the app; it starts listening on its own.
5. Turn Play Protect scanning back on.

Installing over USB (`adb install`) skips steps 1, 3 and 5.

## Where things are

- `App.tsx` – screen, Hotstar filter (`KEYWORD`) and OTP regex (`extractOtp`)
- `android/app/src/main/java/com/otpreader/SmsModule.kt` – reads inbox, listens for incoming SMS
- `android/app/src/main/java/com/otpreader/SmsPackage.kt` – registers the module (added in `MainApplication.kt`)
- `android/app/src/main/AndroidManifest.xml` – `READ_SMS` / `RECEIVE_SMS` permissions
- `android/app/src/main/res/drawable/ic_launcher_foreground.xml` – icon "OTP" text and green dots (vector)
- `android/app/src/main/res/drawable/ic_launcher_background.xml` – icon gradient (`#111827` → `#374151`)
- `android/app/src/main/res/mipmap-anydpi-v26/` – adaptive icon (Android 8+); `mipmap-*/ic_launcher*.png` are the Android 7 fallbacks

Android only; iOS does not allow apps to read SMS.

## Changes

### 2026-10-02

- **Permission help screen** (`App.tsx`): SMS permission is asked once on first launch. If it is blocked, the app shows the unlock steps and an "Open settings" button instead of a one-line error, and starts listening by itself when the user comes back with permission granted (`AppState` listener, `hasSmsPermission()`).
- **App icon**: replaced the default React Native icon with a bold white "OTP" and three green dots on a charcoal gradient. Adaptive (fits any launcher shape) with themed-icon support, plus PNG fallbacks.
- Tried Google's SMS User Consent API (no SMS permission, but an "Allow" popup for every OTP) and reverted it in favour of the one-time permission. `SmsModule.kt`, `AndroidManifest.xml` and `app/build.gradle` are unchanged.
# hotstar-sms-reader
