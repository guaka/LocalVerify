# Tests and coverage

Run commands from the repository root unless otherwise noted.

## Synthetic smoke test

For a safe end-to-end smoke test, generate the synthetic fixtures:

```sh
python3 tools/generate_fixture.py
```

The generated archive contains no real device data. Transfer `Fixtures/synthetic-indicators.stix2` and `Fixtures/synthetic-sysdiagnose.tar.gz` to Files on iOS or local storage accessible to Android's document picker. Import the synthetic indicators first, then the archive, and expect one raw-text lead and one structured lead. Use “Use bundled indicators” afterward to restore the bundled definitions.

## iOS and shared Swift core

Run the Swift package tests with:

```sh
swift test
```

Generate a SwiftPM coverage summary and LCOV file with:

```sh
./tools/coverage.sh
```

Reports are written to:

- `build/coverage-reports/swiftpm-coverage.txt`
- `build/coverage-reports/swiftpm-coverage.lcov`

The SwiftPM report excludes generated SwiftPM test harness files and test-source files so the totals focus on production coverage.

Generate coverage for the iOS UI tests on a simulator with:

```sh
./tools/coverage.sh ios \
  --destination 'platform=iOS Simulator,name=iPhone 17 Pro'
```

The script generates the Files-only UI-test project. The iOS report is written to `build/coverage-reports/ios-ui-coverage.txt`; each run keeps a separate raw result bundle under `build/coverage-reports/ios-run.*/results.xcresult`.

UI regression checks are generated with:

```sh
python3 tools/generate_project.py --local-only --ui-tests
```

Run the generated checks with the `LocalVerifyChecks` scheme in Xcode.

## Android

From `Android/`, run JVM tests and lint with:

```sh
./gradlew :app:testDebugUnitTest :app:lintDebug
```

Run workflow tests only on a disposable emulator without existing Local Verify cases. Select its serial from `adb devices`:

```sh
ANDROID_SERIAL=emulator-5554 ./gradlew :app:connectedDebugAndroidTest
```

These tests use synthetic evidence. Do not use a phone containing real cases as the instrumentation test target. JVM reports are written under `Android/app/build/reports/tests/`; emulator reports are under `Android/app/build/reports/androidTests/`.
