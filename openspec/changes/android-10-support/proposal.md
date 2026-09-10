## Why

The Android app currently excludes Android 10 devices by declaring API 30 as its minimum. Support Android 10 and newer as requested.

## What Changes

- Lower the production Android minimum SDK to API 29 while retaining compile and target SDK 34.
- Document Android 10+ support and verify API compatibility and synthetic workflows.

## Capabilities

### New Capabilities
- `android-platform-support`: Minimum supported Android version and compatibility acceptance criteria.

### Modified Capabilities
None.

## Impact

Android app build configuration, synthetic instrumentation acceptance checks, and current platform documentation. Engine behavior, iOS, and experimental/shared harness targets are unchanged.
