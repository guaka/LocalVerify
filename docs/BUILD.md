# Building Local Verify

Run commands from the repository root unless otherwise noted.

Byte-for-byte reproducible builds are not yet verified. See [current status and next steps](REPRODUCIBLEBUILDS.md).

## iOS

Requires Xcode 16+ and Python 3 for project generation and offline source checks. Python is not included in the app.

```sh
python3 tools/generate_project.py
swift test
xcodebuild -project LocalVerify.xcodeproj \
  -scheme LocalVerify \
  -sdk iphonesimulator \
  -configuration Debug \
  CODE_SIGNING_ALLOWED=NO build
```

Open `LocalVerify.xcodeproj` in Xcode to run the app. Physical-device builds need a development team, a registered device, and the App Group `group.org.mobiletriage.private` configured for both targets.

For a Files-only build without App Groups or the share extension, run `python3 tools/generate_project.py --local-only` and open `LocalVerifyLocal.xcodeproj`. Use Save to Files → On My iPhone, then import in Local Verify. Physical-device installation still requires signing.

Distribute the source with private builds, including LICENSE and third-party notices.

## Android

The native Android app lives in `Android/` and uses Kotlin and Jetpack Compose. It supports Android 11+ (API 30+) and has Scan, Cases, Indicators, and About tabs.

Requires JDK 17+, Android SDK Platform 34, and an SDK location configured through `ANDROID_HOME` or `Android/local.properties`. Android Studio can provide the JDK and SDK. The repository includes the Gradle wrapper; build dependencies may require internet access even though the app processes evidence offline.

From the repository root:

```sh
cd Android
./gradlew :app:assembleDebug
```

The debug APK is written to `Android/app/build/outputs/apk/debug/localverify-debug.apk` (relative to the repository root).

To install on a connected Android 11+ device with USB debugging enabled, run from `Android/`:

```sh
./gradlew :app:installDebug
```

Android supports archive selection through the system document picker and incoming share/open intents. Cases and incomplete results are stored locally; report ZIP exports include JSON and HTML, with the original archive included only when selected.

Physical-device and manufacturer-specific collection guidance still need validation, and full upstream Android-MVT parity is not established. See the [Android implementation notes](ANDROID.md) for setup and release-signing details, and the [Android validation matrix](ANDROID-VALIDATION-MATRIX.md) for recorded test coverage and remaining gaps.
