## Purpose

Define the supported Android versions for installing and using LocalVerify's local diagnostic workflow.

## ADDED Requirements

### Requirement: Android 10 minimum platform
The production Android app SHALL support Android 10 (API 29) and newer and SHALL declare API 29 as its minimum supported SDK.

#### Scenario: Install on the minimum supported platform
- **WHEN** the production APK is installed on a disposable Android 10 emulator
- **THEN** installation succeeds and the app launches without an unsupported API error

#### Scenario: Synthetic diagnostic workflow on supported platforms
- **WHEN** a synthetic diagnostic archive is imported and analyzed on Android 10 or a newer supported version
- **THEN** the user can review and export the report using the existing local-only workflow
- **AND** screenshot protection and backup exclusion remain enabled

#### Scenario: Older platforms are excluded
- **WHEN** the APK's platform compatibility metadata is inspected
- **THEN** its minimum SDK is API 29, excluding installation on Android 9 and older
