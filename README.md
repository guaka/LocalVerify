# LocalVerify

> Shared-engine migration requirements and named legacy differences are maintained in [the engine contract guide](docs/ENGINE-EXPERIMENT.md). Existing implementation and historical validation notes below remain platform-specific.

<img src="iOS/App/Assets.xcassets/AppIcon.appiconset/app-icon.png" alt="LocalVerify app icon" width="128">

LocalVerify is an experimental, local-only app for iOS 17+ and Android 11+ for importing and reviewing diagnostic archives: iOS sysdiagnose files and Android bug reports. It has no backend, telemetry, or evidence upload.

The app bundles a snapshot of Amnesty/MVT indicators for offline analysis. It is not full MVT parity and does not provide comprehensive current-spyware coverage. Results are leads for investigation, not proof of compromise.

## Using the app

### iOS

1. Follow the in-app guide to collect a sysdiagnose using Apple-supported settings.
2. Save it to `On My iPhone > LocalVerify > Imports`.
3. Use bundled indicators, or import a local STIX indicator file before importing the archive.
4. Review findings and export a report ZIP manually.

Imported iOS cases keep their indicator set so resumed analysis is reproducible.

### Android

1. Follow the in-app collection guidance to create a bug report. The available settings and collection steps vary by manufacturer.
2. Save the archive locally, then select it in Scan or explicitly share/open it with LocalVerify. ZIP and gzip-compressed tar archives are supported.
3. Use the bundled indicators, or import a local STIX2 bundle in Indicators before creating the case.
4. Start analysis and keep the app in the foreground with the screen unlocked. Switching apps or locking the screen stops analysis; incomplete results remain available, but starting again scans from the beginning.
5. Open Cases to review findings, errors, and skipped files. Prepare a report ZIP and share it explicitly; including the original archive is optional.

On both platforms, case storage is excluded from automatic backup, and deleting a case deletes its stored exports. Evidence upload is never automatic. Copies saved or shared outside the app remain separate.

## Supported data and limits

Both apps scan supported UTF-8 `.ips`, `.json`, `.crash`, `.log`, and `.txt` archive entries. iOS accepts gzip-compressed tar archives; Android also accepts ZIP containers. Binary unified logs, binary plists, links, and other unsupported entries are not analyzed as text.

Limits include 8 GiB expanded input, 100,000 archive entries, 16 MiB per parsed file, and 10,000 findings. Exports use standard ZIP and must remain under 4 GiB.

STIX matching is intentionally limited to single equality expressions for domains, URLs, processes, and file paths/names. Matching is case-insensitive for domains and exact for other values. No malicious-domain lookups or network requests are performed.

See the detailed [verification](docs/VERIFICATION.md), [report contract](docs/REPORT.md), [privacy](docs/PRIVACY.md), and [indicator update](docs/INDICATOR_UPDATES.md) documentation for more detail.

## Comparison with related tools

Reviewed 2026-09-07 against linked documentation and the current implementation. This compares scope, not measured detection effectiveness. Commercial features vary by offering.

Bugbane added from its website and repository documentation on 2026-09-08.

Licence: 🟢 Open source · 🟡 Restricted or unverified (see label) · 🔴 Proprietary. **🔴 Experimental** flags maturity; colors are not detection scores.

Execution: 🟢 On phone · 🟡 Separate computer or phone workflow unverified · 🔴 Shared with service. Library rows describe the listed tool workflow, not every possible embedding.

| Tool | Platform | Focus | Evidence / execution | Licence |
| --- | --- | --- | --- | --- |
| **LocalVerify — 🔴 Experimental** | iOS, Android | Limited STIX matching; coverage unvalidated in real-world compromise cases | 🟢 Diagnostics; offline on phone| 🟢 [AGPL-3.0+](LICENSE) |
| [Bugbane](https://bugbane.org/) — Beta | Android | On-device acquisition and spyware/stalkerware IOC checks | 🟢 Same-phone Wireless Debugging; encrypted exports; offline workflow unverified | 🟢 [GPL-3.0-only](https://github.com/osservatorionessuno/bugbane#license) |
| [IsMyPhonePwned](https://ismyphonepwned.com/) — Beta | iOS, Android | Diagnostic parsing and detection rules | 🟡 Sysdiagnose/bugreport; browser-local; phone/offline unverified | 🟢 [Apache-2.0 parsers](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) / 🟡 [Web licence unverified](https://github.com/IsMyPhonePwned/ismyphonepwned.github.io) |
| [Hypatia (maintained fork)](https://github.com/MaintainTeam/Hypatia) | Android | Malware file/app signatures; no diagnostic-log analysis | 🟢 On-phone scan; offline after signature download | 🟢 [Free/open source](https://github.com/MaintainTeam/Hypatia/blob/stable/LICENSE) |
| [Panda Sysdiagnose](https://apps.apple.com/fi/app/sysdiagnose/id6795409832) | iOS | Device health; no documented spyware checks | 🟢 Sysdiagnose; on-device parsing/storage claimed | 🟡 No free-software licence found; free download |
| [Amnesty MVT](https://github.com/mvt-project/mvt) | iOS, Android | Investigator-led spyware analysis | 🟡 Backups, filesystems, Android acquisitions; desktop CLI| 🟡 [MVT 1.1](https://github.com/mvt-project/license/blob/main/MVT%20License%201.1.txt) |
| [EC SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose) | iOS | Deep parsing, YARA, timelines | 🟡 Sysdiagnose; desktop| 🟢 [EUPL-1.2](https://github.com/EC-DIGIT-CSIRC/sysdiagnose/blob/main/LICENSE.txt) |
| [iLEAPP](https://github.com/abrignoni/iLEAPP) | iOS | Broad artifact parsing and reports | 🟡 Backups/extractions; desktop GUI/CLI| 🟢 [MIT](https://github.com/abrignoni/iLEAPP/blob/main/LICENSE) |
| [ALEAPP](https://github.com/abrignoni/ALEAPP) | Android | Broad artifact parsing and reports | 🟡 Extractions; desktop GUI/CLI| 🟢 [MIT](https://github.com/abrignoni/ALEAPP/blob/main/LICENSE) |
| [AndroidQF](https://github.com/mvt-project/androidqf) | Android | Collection for MVT; no detection | 🟡 USB to computer; optional encrypted archive| 🟡 [MVT 1.1](https://github.com/mvt-project/license/blob/main/MVT%20License%201.1.txt) |
| [Mandiant UnifiedLogs](https://github.com/mandiant/macos-UnifiedLogs) | iOS, macOS | Binary log decoding | 🟡 Unified logs; local Rust library/CLI| 🟢 [Apache-2.0](https://github.com/mandiant/macos-UnifiedLogs/blob/main/LICENSE) |
| [iShutdown](https://github.com/KasperskyLab/iShutdown) | iOS | Shutdown-log anomalies | 🟡 Sysdiagnose/Shutdown.log; desktop scripts| 🟢 [MIT](https://github.com/KasperskyLab/iShutdown/blob/master/License.txt) |
| [Triangle Check](https://github.com/KasperskyLab/triangle_check) | iOS | Operation Triangulation traces | 🟡 iTunes backups; desktop CLI| 🟢 [MIT](https://github.com/KasperskyLab/triangle_check/blob/main/License.txt) |
| [SCNR](https://github.com/shindan-io/scnr) | Format-based | Structured-file parsing, not detection | 🟡 Plists, SQLite, archives; local Rust tools| 🟢 [Apache-2.0](https://github.com/shindan-io/scnr/blob/main/LICENSE) |
| [BugBay](https://github.com/hidden-investigations/bugbay) | Web apps | Vulnerable training labs; no mobile forensics | 🟡 Linux/Docker; downloads and optional LAN exposure| 🟢 [Apache-2.0](https://github.com/hidden-investigations/bugbay/blob/main/LICENSE) |
| [iMazing Analyzer](https://imazing.com/spyware-analyzer) | iOS | Guided spyware checks; free feature | 🟡 Backups; Mac/Windows; network features| 🔴 [App](https://imazing.com/uploads/iMazing-EULA.pdf) + 🟡 [MVT analyzer](https://github.com/DigiDNA/iMazing-Malware-Analyzer) |
| [iVerify Enterprise](https://welcome.iverify.io/hubfs/iVerify-Mobile-Threat-Detection-Scanning-Capabilities-for-Enterprise.pdf) | iOS, Android | Commercial scans and monitoring | 🔴 Diagnostics shared with service; telemetry varies| 🔴 Proprietary |

MVT uses [AndroidQF for Android acquisition](https://github.com/mvt-project/mvt/blob/main/docs/android/methodology.md); its [sysdiagnose checks require forensic plugins](https://docs.mvt.re/en/latest/ios/sysdiagnose/). Desktop analysis can be local: [iMazing](https://imazing.com/guides/detect-pegasus-and-other-spyware-on-iphone) processes backups on the computer but uses the internet for indicators and shortened-link expansion. LocalVerify's distinction is keeping analysis on the phone without automatic uploads.

Close workflow matches: [Bugbane](https://github.com/osservatorionessuno/bugbane) performs Android acquisition and IOC analysis on the same phone through Wireless Debugging, with encrypted evidence exports. Its developers warn that use can be detected by spyware/stalkerware, advise against use where physical harm is a risk, and do not yet recommend it for non-technical users. Encryption is not redaction. IsMyPhonePwned imports both diagnostic formats and applies [detection rules](https://ismyphonepwned.com/bugreport-status.html), but browser-local processing does not establish reliable offline operation on the affected phone. Its web app fetches assets/rules; a complete airplane-mode workflow and the whole web-app licence remain unverified. Panda documents local diagnostic storage, not spyware detection; its offline operation was not tested. These are documentation/source comparisons, not hands-on validation.

MVT's consent restriction makes it source-available rather than open source under the [Open Source Definition](https://opensource.org/osd). App code, dependencies and indicator datasets have separate terms; free of charge does not mean open source.

### LocalVerify tradeoffs

See the [feature roadmap](docs/FEATURE-ROADMAP.md) for iOS/Android implementation status, free-software references, and the proposed iOS-first expansion.

See [why LocalVerify does not embed MVT](docs/WHY-NOT-MVT.md) for the workflow, integration and licensing rationale, and the resulting coverage tradeoff.

🟢 Strength · 🟡 Gap · 🔴 Experimental / unvalidated · 🔵 Tradeoff.

| Area | Current position |
| --- | --- |
| 🟢 Privacy | Offline on-device analysis; explicit sharing; no telemetry |
| 🔴 Maturity | Experimental; synthetic tests do not establish real-world detection effectiveness |
| 🟡 Coverage | Selected text/JSON/crash entries and limited STIX matching; binary unified logs and plists skipped |
| 🟡 Accessibility | Guided interface, but installation/signing and physical-device validation remain barriers |
| 🟢 Handoff | Source context, hashes, indicator snapshots and reports support expert review |
| 🔵 Trust | A compromised OS can interfere with evidence and analysis; no-match results cannot prove safety |

These are current tradeoffs, not committed features. See [privacy](docs/PRIVACY.md), [hardening](docs/HARDEN.md), [verification](docs/VERIFICATION.md) and [Android validation](docs/ANDROID-VALIDATION-MATRIX.md). Development uses synthetic evidence under [AGENTS.md](AGENTS.md).

## Build and test

See the [build instructions](docs/BUILD.md) for iOS and Android setup, compilation, signing, and installation.

See the [testing guide](docs/TESTING.md) for Swift, iOS UI, and Android tests and coverage reports.

## License

LocalVerify's project-owned source is licensed under the GNU Affero General Public
License, version 3 or (at your option) any later version (`AGPL-3.0-or-later`).
See [LICENSE](LICENSE) for the full terms. The software is provided without warranty.

Third-party components and indicator datasets retain their own licenses and notices;
see [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md). Distributions must provide
recipients access to the corresponding source as required by the AGPL.

## Shared-engine development

Install the pinned development CLI with `npm ci`, then use `npm run spec --` and `npm run spec:validate`. The wrapper disables OpenSpec telemetry. Repository-local assistant skills are in `.agents/skills/`.

The [engine contract guide](docs/ENGINE-EXPERIMENT.md) records shared requirements and legacy differences. The [architecture decision](docs/ADR-001-shared-engine.md) compares the isolated Rust and Kotlin prototypes and stages the future migration. Production apps continue to use their existing engines.

The selected KMP engine is promoted in [Shared](Shared/README.md), with typed results, bounded token indexing, publisher metadata and legacy cache handling. The original experiment remains frozen. Archive processing, report compatibility and activation in both native apps remain separate OpenSpec stages.
