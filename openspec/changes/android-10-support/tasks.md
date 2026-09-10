## 1. Implementation

- [x] 1.1 Lower production minSdk to 29; verify the built APK declares API 29 and run Android lint and unit tests.
- [x] 1.2 Update current Android support documentation and OpenSpec context; verify current support references say Android 10+.
- [x] 1.3 Extend synthetic workflow assertions for installed minimum SDK and backup exclusion; compile the instrumentation APK.

## 2. Verification

- [ ] 2.1 Run synthetic workflow instrumentation on disposable API 29 and newer emulators and record results; keep pending if a required emulator is unavailable.
- [x] 2.2 Run strict OpenSpec validation and record remaining verification limitations before any archive.

Validation (2026-09-10): `:app:assembleDebug :app:testDebugUnitTest :app:lintDebug :app:assembleDebugAndroidTest` passed; 19 unit tests, zero failures. APK metadata confirms minimum SDK 29 and target SDK 34. Strict OpenSpec validation passed all 12 items. No devices were connected and only an API 36 system image was installed, so instrumentation runtime validation remains pending. This change is not archived.
