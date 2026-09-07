# Local Verify

> Shared-engine migration requirements and named legacy differences are maintained in [the engine contract guide](docs/ENGINE-EXPERIMENT.md). Existing implementation and historical validation notes below remain platform-specific.

<img src="iOS/App/Assets.xcassets/AppIcon.appiconset/app-icon.png" alt="Local Verify app icon" width="128">

Local Verify is an experimental, local-only app for iOS 17+ and Android 11+ for importing and reviewing diagnostic archives: iOS sysdiagnose files and Android bug reports. It has no backend, telemetry, or evidence upload.

The app bundles a snapshot of Amnesty/MVT indicators for offline analysis. It is not full MVT parity and does not provide comprehensive current-spyware coverage. Results are leads for investigation, not proof of compromise.

## Build

Byte-for-byte reproducible builds are not yet verified. See [current status and next steps](docs/REPRODUCIBLEBUILDS.md).

### iOS

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

### Android

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

Physical-device and manufacturer-specific collection guidance still need validation, and full upstream Android-MVT parity is not established. See the [Android implementation notes](docs/ANDROID.md) for setup and release-signing details, and the [Android validation matrix](docs/ANDROID-VALIDATION-MATRIX.md) for recorded test coverage and remaining gaps.

## Tests and coverage

### iOS and shared Swift core

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

### Android

From `Android/`, run JVM tests and lint with:

```sh
./gradlew :app:testDebugUnitTest :app:lintDebug
```

Run workflow tests only on a disposable emulator without existing Local Verify cases. Select its serial from `adb devices`:

```sh
ANDROID_SERIAL=emulator-5554 ./gradlew :app:connectedDebugAndroidTest
```

These tests use synthetic evidence. Do not use a phone containing real cases as the instrumentation test target. JVM reports are written under `Android/app/build/reports/tests/`; emulator reports are under `Android/app/build/reports/androidTests/`.

## Using the app

### iOS

1. Follow the in-app guide to collect a sysdiagnose using Apple-supported settings.
2. Save it to `On My iPhone > Local Verify > Imports`.
3. Use bundled indicators, or import a local STIX indicator file before importing the archive.
4. Review findings and export a report ZIP manually.

Imported iOS cases keep their indicator set so resumed analysis is reproducible.

### Android

1. Follow the in-app collection guidance to create a bug report. The available settings and collection steps vary by manufacturer.
2. Save the archive locally, then select it in Scan or explicitly share/open it with Local Verify. ZIP and gzip-compressed tar archives are supported.
3. Use the bundled indicators, or import a local STIX2 bundle in Indicators before creating the case.
4. Start analysis and keep the app in the foreground with the screen unlocked. Switching apps or locking the screen stops analysis; incomplete results remain available, but starting again scans from the beginning.
5. Open Cases to review findings, errors, and skipped files. Prepare a report ZIP and share it explicitly; including the original archive is optional.

On both platforms, case storage is excluded from automatic backup, and deleting a case deletes its stored exports. Evidence upload is never automatic. Copies saved or shared outside the app remain separate.

### Synthetic smoke test

For a safe end-to-end smoke test, generate the synthetic fixtures:

```sh
python3 tools/generate_fixture.py
```

The generated archive contains no real device data. Transfer `Fixtures/synthetic-indicators.stix2` and `Fixtures/synthetic-sysdiagnose.tar.gz` to Files on iOS or local storage accessible to Android's document picker. Import the synthetic indicators first, then the archive, and expect one raw-text lead and one structured lead. Use “Use bundled indicators” afterward to restore the bundled definitions.

## Supported data and limits

Both apps scan supported UTF-8 `.ips`, `.json`, `.crash`, `.log`, and `.txt` archive entries. iOS accepts gzip-compressed tar archives; Android also accepts ZIP containers. Binary unified logs, binary plists, links, and other unsupported entries are not analyzed as text.

Limits include 8 GiB expanded input, 100,000 archive entries, 16 MiB per parsed file, and 10,000 findings. Exports use standard ZIP and must remain under 4 GiB.

STIX matching is intentionally limited to single equality expressions for domains, URLs, processes, and file paths/names. Matching is case-insensitive for domains and exact for other values. No malicious-domain lookups or network requests are performed.

See the detailed [verification](docs/VERIFICATION.md), [report contract](docs/REPORT.md), [privacy](docs/PRIVACY.md), and [indicator update](docs/INDICATOR_UPDATES.md) documentation for more detail.

## Comparison with related tools

Reviewed on 2026-09-07 against the linked project documentation and Local Verify's current implementation. This is a comparison of documented scope, not a controlled detection benchmark or an independent security audit. Commercial capabilities vary by offering and configuration. Links to upstream projects and licences are provided for verification; their inclusion does not imply endorsement or affiliation.

Local Verify focuses on reviewing selected mobile diagnostics without requiring a computer or evidence upload for analysis. Installation and diagnostic collection remain separate prerequisites. Its privacy design does not establish superior spyware detection, and a scan without matches does not establish that a device is uncompromised.

### Capabilities, privacy and licensing

Licence dots: 🟢 Open source · 🟡 Source available with use restrictions · 🔴 Proprietary. Products with differently licensed components show a dot for each component. These indicate licence categories, not safety or detection ratings; each cell also describes the category in words.

| Project / product | Devices examined | Execution and audience | Evidence and analysis | Privacy and connectivity | Source and licence | Relationship to Local Verify |
| --- | --- | --- | --- | --- | --- | --- |
| Local Verify | iOS and Android | On the phone; guided interface | Imports sysdiagnose or bug reports; selected text, JSON and crash records; limited STIX equality matching | On-device analysis; bundled or manually imported indicators; no telemetry or automatic uploads; explicit sharing | 🟢 Open source; [AGPL-3.0-or-later](LICENSE) for project code | Experimental phone-only workflow; narrow artifact coverage and unestablished real-world detection effectiveness |
| [Amnesty MVT](https://github.com/mvt-project/mvt) | iOS and Android | Computer; investigators using command-line tools | iOS backups and filesystem extractions; Android acquisitions and bug reports; artifact-specific checks, indicators and plugins | Acquired evidence is processed on the analyst's computer; network use depends on enabled features and configuration | 🟡 Source available; [MVT License 1.1](https://github.com/mvt-project/license/blob/main/MVT%20License%201.1.txt), with consent restrictions | Primary forensic benchmark; broader analysis and research ecosystem, with greater technical and workstation requirements |
| [EC Sysdiagnose Analysis Framework (SAF)](https://github.com/EC-DIGIT-CSIRC/sysdiagnose) | iOS | Computer; incident responders and researchers | Dedicated sysdiagnose parsers, process comparisons, structured records, YARA, timelines and case management | Evidence and derived records reside in the configured analysis environment | 🟢 Open source; [EUPL-1.2](https://github.com/EC-DIGIT-CSIRC/sysdiagnose/blob/main/LICENSE.txt), copyleft | Closest iOS diagnostic-parser comparison; richer artifact interpretation, requiring more technical support |
| [iMazing Spyware Analyzer](https://imazing.com/spyware-analyzer) | iOS and iPadOS | Mac or Windows; guided desktop interface | Backup-based spyware and stalkerware checks; MVT-derived analysis, additional checks and custom STIX support | Backup analysis is local to the computer; documented internet use for indicators and shortened-link expansion | 🔴 Proprietary host app under [EULA](https://imazing.com/uploads/iMazing-EULA.pdf); 🟡 [analyzer source](https://github.com/DigiDNA/iMazing-Malware-Analyzer) under MVT licence; analyzer feature is free of charge | Accessible desktop alternative with established distribution; requires evidence on a computer |
| [iVerify Enterprise](https://welcome.iverify.io/hubfs/iVerify-Mobile-Threat-Detection-Scanning-Capabilities-for-Enterprise.pdf) | iOS and Android | Phone apps plus organizational analysis and management services | Vendor-documented forensic scans, behavioral analysis and ongoing monitoring; capabilities depend on offering | Forensic scans share diagnostics with iVerify; extended protection uploads telemetry; deployment options need separate evaluation | 🔴 Proprietary commercial product | Commercial service comparison; adds monitoring and vendor analysis with a different evidence-sharing and trust model |
| [iLEAPP](https://github.com/abrignoni/iLEAPP) | iOS and iPadOS | Desktop GUI or command line; forensic examiners | Backups, filesystem extractions and archives; extensive artifact modules; HTML, TSV, timeline and other outputs | Workstation evidence processing; storage and report handling are operator-managed | 🟢 Open source; [MIT](https://github.com/abrignoni/iLEAPP/blob/main/LICENSE), permissive | Benchmark for artifact breadth and reporting; general forensic parsing does not itself imply spyware detection |
| [ALEAPP](https://github.com/abrignoni/ALEAPP) | Android | Desktop GUI or command line; forensic examiners | Application and system artifacts, databases and protobuf records; HTML, TSV and timeline reporting | Workstation evidence processing; operational protection depends on deployment | 🟢 Open source; [MIT](https://github.com/abrignoni/ALEAPP/blob/main/LICENSE), permissive | Android parser benchmark; many artifacts require richer extractions than a bug report provides |
| [AndroidQF](https://github.com/mvt-project/androidqf) | Android | Mac, Windows or Linux connected over USB; collectors and investigators | Collects bug reports, logs, package information and optional APKs or backups for later analysis | Transfers evidence to a computer; supports recipient-encrypted acquisition archives | 🟡 Source available; MVT License 1.1 | Acquisition companion to MVT; does not perform detection; Local Verify imports user-provided archives |
| [Mandiant macos-UnifiedLogs](https://github.com/mandiant/macos-UnifiedLogs) | Apple unified logs, including iOS and macOS | Rust library and command-line example; developers and analysts | Decodes binary unified logs into records with timestamps, processes, subsystems and messages | Local parsing; output can be large | 🟢 Open source; [Apache-2.0](https://github.com/mandiant/macos-UnifiedLogs/blob/main/LICENSE), permissive | Implementation reference for a Local Verify coverage gap; a parser component rather than a complete spyware scanner |
| [Kaspersky iShutdown](https://github.com/KasperskyLab/iShutdown) | iOS | Computer; Python scripts for investigators | Detects anomalous entries in sysdiagnose Shutdown.log artifacts; companion scripts parse logs and summarize shutdown activity | Processes supplied archives or logs on the computer | 🟢 Open source; [MIT terms](https://github.com/KasperskyLab/iShutdown/blob/master/License.txt), permissive | Focused reference for artifact-specific anomaly checks; depends on the presence and format of shutdown logs and is not comprehensive spyware detection |
| [Kaspersky Triangle Check](https://github.com/KasperskyLab/triangle_check) | iOS | Computer; command-line tool | Checks iTunes backups, including password-encrypted backups, for Operation Triangulation traces and suspicious event combinations | Processes a supplied backup on the computer | 🟢 Open source; [MIT terms](https://github.com/KasperskyLab/triangle_check/blob/main/License.txt), permissive | Campaign-specific comparison showing the value of correlated artifacts; does not provide general spyware coverage or on-device analysis |
| [SCNR](https://github.com/shindan-io/scnr) | Format-oriented; relevant to iOS sysdiagnose and other forensic files | Rust command-line tool and libraries; developers and analysts | Scans heterogeneous files and converts supported structured formats, including plists and SQLite, into queryable JSON | Processes supplied files locally; integration determines evidence handling | 🟢 Open source; [Apache-2.0](https://github.com/shindan-io/scnr/blob/main/LICENSE), permissive | Parser architecture reference for expanding structured coverage; does not supply a complete mobile spyware verdict |
| [BugBay](https://github.com/hidden-investigations/bugbay) | Vulnerable web applications | Linux and Docker; security students and instructors | Launches and manages intentionally vulnerable training targets | Loopback defaults, optional network exposure, upstream image and code downloads | 🟢 Open source; [Apache-2.0](https://github.com/hidden-investigations/bugbay/blob/main/LICENSE), permissive | Peripheral training tool; no mobile diagnostic or spyware-analysis capability |

MVT's current Android workflow separates acquisition from analysis: AndroidQF collects and MVT analyzes. Direct ADB analysis was removed; optional VirusTotal APK-hash lookups are disabled by default. See [MVT's Android methodology](https://github.com/mvt-project/mvt/blob/main/docs/android/methodology.md). Its [sysdiagnose command](https://docs.mvt.re/en/latest/ios/sysdiagnose/) depends on installed forensic plugins; built-in metadata collection alone is not a spyware check.

iMazing's [analysis guide](https://imazing.com/guides/detect-pegasus-and-other-spyware-on-iphone) distinguishes local backup processing from internet use and explains its separate analyzer licence. MVT and AndroidQF are labelled **source available, consent-restricted** here because public source access is distinct from the unrestricted fields of use required by the [Open Source Definition](https://opensource.org/osd). Free of charge describes price, not licensing. Third-party dependencies and indicator datasets retain their own terms; these rows do not establish that code or data can be combined under a single licence.

### What the comparison means for Local Verify

The observations below describe current tradeoffs and possible development priorities, not committed features. Behavioral changes follow the repository's OpenSpec workflow.

Review dots: 🟢 Existing strength · 🟡 Coverage or validation gap · 🔵 Design tradeoff · 🟠 Trust limitation. These are qualitative labels for Local Verify, not comparative scores.

| Review dimension | What the comparison shows | Implication for Local Verify |
| --- | --- | --- |
| 🟡 Gap: iOS evidence coverage | Backups, filesystem extractions and sysdiagnose contain different evidence; MVT, iMazing and SAF answer overlapping but different questions | Identify the archive entries and artifact classes checked; platform support alone does not describe forensic coverage |
| 🟡 Gap: Binary formats | Local Verify currently skips binary unified logs and binary plists; specialist parsers can interpret evidence beyond text matching | Selected binary-format support is a potential coverage improvement, subject to resource budgets and validation |
| 🟡 Gap: Detection depth | Exact indicator matching finds known strings; artifact-specific interpretation supplies additional context | Keep structured findings distinct from incidental text matches and preserve explicit coverage gaps |
| 🟢 Strength: Offline operation | Desktop tools can also process evidence locally | Local Verify's distinction is analysis that can remain on the phone, without requiring a computer or upload |
| 🟠 Limitation: Trust in the device | A compromised phone can falsify diagnostics or interfere with analysis; a trusted workstation can independently check computation but cannot restore suppressed evidence | On-device processing reduces transfers but does not guarantee trustworthy execution under a compromised OS |
| 🟡 Gap: User accessibility | iMazing provides a distributed desktop wizard; MVT and SAF require more expertise | Local Verify's build and signing requirements and outstanding physical-device validation remain practical barriers |
| 🟡 Gap: Maturity and validation | Established tools have broader operational histories; Local Verify's recorded checks chiefly establish synthetic behavior and simulator or emulator workflows | Do not claim comparative detection effectiveness without representative evaluation and known expected results |
| 🟢 Strength: Expert handoff | MVT, SAF and LEAPP support detailed investigator workflows | Preserve source context, hashes, indicator provenance, skipped and error records, and explicit export choices |
| 🔵 Tradeoff: Licensing | Permissive, copyleft, source-available and proprietary terms differ; free of charge is a separate property | Review application code, parser dependencies and indicator datasets separately before reuse |
| 🔵 Tradeoff: Product focus | iVerify emphasizes organizational monitoring; Local Verify emphasizes user-controlled archive review | Keep the first-pass review understandable and explicit about the evidence actually examined |

See [privacy](docs/PRIVACY.md), [hardening](docs/HARDEN.md), [verification](docs/VERIFICATION.md) and the [Android validation matrix](docs/ANDROID-VALIDATION-MATRIX.md) for Local Verify's implementation evidence and remaining gaps. Real phone evidence stays on the phone during development unless an exception is explicitly authorized under [AGENTS.md](AGENTS.md).

## License

Local Verify's project-owned source is licensed under the GNU Affero General Public
License, version 3 or (at your option) any later version (`AGPL-3.0-or-later`).
See [LICENSE](LICENSE) for the full terms. The software is provided without warranty.

Third-party components and indicator datasets retain their own licenses and notices;
see [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md). Distributions must provide
recipients access to the corresponding source as required by the AGPL.

## Shared-engine development

Install the pinned development CLI with `npm ci`, then use `npm run spec --` and `npm run spec:validate`. The wrapper disables OpenSpec telemetry. Repository-local assistant skills are in `.agents/skills/`.

The [engine contract guide](docs/ENGINE-EXPERIMENT.md) records shared requirements and legacy differences. The [architecture decision](docs/ADR-001-shared-engine.md) compares the isolated Rust and Kotlin prototypes and stages the future migration. Production apps continue to use their existing engines.

The selected KMP engine is promoted in [Shared](Shared/README.md), with typed results, bounded token indexing, publisher metadata and legacy cache handling. The original experiment remains frozen. Archive processing, report compatibility and activation in both native apps remain separate OpenSpec stages.
