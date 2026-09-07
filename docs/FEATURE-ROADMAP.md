# LocalVerify feature roadmap

Reviewed 2026-09-07. This is a feature-level roadmap, not an exhaustive parity audit or a committed implementation specification. [OpenSpec specifications](../openspec/specs/) remain authoritative for specified behavior.

## Direction

Long-term, bring the relevant capabilities of existing free-software mobile-forensics projects into LocalVerify while preserving offline, on-phone analysis and user-controlled evidence sharing.

Start with a smaller **iOS sysdiagnose release**: structured text/crash parsing, curated bundled checks, and investigation views. Track Android separately. This roadmap does not select an engine or claim detection parity.

## Feature status and references

🟢 Implemented · 🟡 Partial · ⚪ Not implemented · — Not applicable

Statuses describe current LocalVerify functionality, not validated spyware-detection effectiveness. References identify projects offering the capability, not necessarily on-phone or through the same inputs. A dash in the reference column means no equivalent was verified here. Shared-engine components not activated in production do not count as implemented app features.

| Feature | iOS today | Android today | Free-software references | Plan |
| --- | --- | --- | --- | --- |
| Import diagnostic archives | 🟢 Sysdiagnose `.tar.gz` | 🟢 Bugreport ZIP / gzip-tar | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP parsers](https://github.com/IsMyPhonePwned) | Retain; expand structured parsing |
| Offline, on-phone processing | 🟢 | 🟢 | [Hypatia](https://github.com/MaintainTeam/Hypatia): Android file scanning | Required throughout |
| Private evidence storage; explicit export | 🟢 | 🟢 | — Exact equivalent not verified | Preserve |
| Archive hashing and safety limits | 🟢 | 🟢 | — Equivalent safeguards not audited | Preserve; test every new parser |
| Text and JSON indicator matching | 🟢 Limited patterns | 🟢 Limited patterns | — Exact matching semantics not compared | Retain alongside new checks |
| Import local STIX indicators | 🟢 | 🟢 | — No additional free-software equivalent verified here | Retain |
| Structured crash analysis | 🟡 Generic JSON/text matching | 🟡 Generic matching | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP parsers](https://github.com/IsMyPhonePwned) | **First iOS release** |
| Process inventory | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP parsers](https://github.com/IsMyPhonePwned) | **First iOS release:** recognized text records |
| Network observations | 🟡 Indicator hits only | 🟡 Indicator hits only | [IMPP parsers](https://github.com/IsMyPhonePwned) | **First iOS release:** supported text sources |
| App inventory and permission analysis | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP Android parser](https://github.com/IsMyPhonePwned/bugreport-extractor-library) | Later; include installation history |
| Curated diagnostic detection rules beyond STIX | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP sysdiagnose parser](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) | **First iOS release:** bundled, versioned checks |
| Sigma YAML rule import/evaluation | ⚪ | ⚪ | [IMPP sysdiagnose parser](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) | Later; explicit supported subset |
| Searchable event timeline | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose): timeline generation | **First iOS release:** supported timestamped records |
| Dedicated process/network/crash views | ⚪ | ⚪ | IMPP parsers supply structured data; web UI licence unverified | **First iOS release:** native views |
| Finding explanations and source excerpts | 🟢 | 🟢 | [IMPP sysdiagnose parser](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library): rule matches | Extend parser/rule provenance |
| Parser-by-parser coverage summary | 🟡 File-level reporting | 🟡 File-level reporting | [IMPP sysdiagnose parser](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) | **First iOS release** |
| Binary plist analysis | ⚪ | — | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [iLEAPP](https://github.com/abrignoni/iLEAPP) | Later iOS |
| SQLite artifact analysis | ⚪ | ⚪ | [iLEAPP](https://github.com/abrignoni/iLEAPP), [ALEAPP](https://github.com/abrignoni/ALEAPP) | Later |
| Apple binary unified-log decoding | ⚪ | — | [Mandiant UnifiedLogs](https://github.com/mandiant/macos-UnifiedLogs) | Later iOS |
| Android intrusion-log analysis | — | ⚪ Dedicated support | IMPP advertises support; parser licensing needs verification | Later Android |
| Backup analysis / APK inspection | ⚪ Backups | ⚪ Backups/APKs | [iLEAPP](https://github.com/abrignoni/iLEAPP): supported iOS artifacts; [JADX](https://github.com/skylot/jadx): APK/DEX | Long-term; separate input and acquisition requirements |
| USB acquisition / live capture | ⚪ | ⚪ | [libimobiledevice](https://github.com/libimobiledevice/libimobiledevice): iOS communication/acquisition | Separate feasibility track; not same-phone analysis |
| YARA scanning | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose) | Later |
| Shutdown-log anomaly checks | ⚪ | — | [iShutdown](https://github.com/KasperskyLab/iShutdown) | Later iOS |
| Operation Triangulation traces | ⚪ | — | [Triangle Check](https://github.com/KasperskyLab/triangle_check) | Later; suitable backup evidence required |
| Broad app-artifact reports | ⚪ | ⚪ | [iLEAPP](https://github.com/abrignoni/iLEAPP), [ALEAPP](https://github.com/abrignoni/ALEAPP) | Long-term |
| Offline file/app malware signatures | ⚪ | ⚪ | [Hypatia](https://github.com/MaintainTeam/Hypatia) | Later Android; iOS access constrained |

## First iOS release

1. **Structured records and coverage:** parse supported crash reports and recognized process/network text formats. Preserve source locations and original timestamps; explicitly report unavailable, malformed and unsupported evidence. Do not infer a complete app inventory from process names.
2. **Bundled checks:** add curated, freely licensed checks alongside existing STIX matching. Preserve rule versions and provenance; separate diagnostic observations from suspicious findings. User-imported Sigma rules are deferred.
3. **Investigation views:** provide an overview, process/network/crash lists, searchable timeline, and finding-to-evidence navigation. Follow [Apple's Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/). No-match results must never be presented as proof of safety.
4. **Persistence and exports:** retain structured results and rule snapshots privately, include them in explicit exports, and preserve readability of existing cases.

## Verification and boundaries

- Test synthetic positive, negative, malformed, oversized, duplicate and interrupted-analysis cases.
- Verify timestamp handling, coverage reporting, historical-case compatibility, escaped exports and operation with networking unavailable.
- Never transfer actual phone evidence for development without explicit permission; follow [AGENTS.md](../AGENTS.md).
- Create OpenSpec scenarios, design and tasks before implementation; mark features implemented only after integration and verification.
- Keep the existing shared-engine migration separate until an implementation approach is selected. See the [engine contract guide](ENGINE-EXPERIMENT.md).
- Treat broader coverage as an aspiration, not an unconditional promise: phone sandboxing, available evidence, resource limits and licensing constrain feasibility.
- “IMPP parsers” refers to the Apache-2.0 [sysdiagnose](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) and [bugreport](https://github.com/IsMyPhonePwned/bugreport-extractor-library) components, not blanket approval of the entire IsMyPhonePwned project. Review each dependency and rule collection before reuse; component licensing does not establish the licence of the web UI or every rule.
- MVT and AndroidQF remain methodology references, but are excluded from the free-software column under the FSF definition discussed in the [licensing rationale](WHY-NOT-MVT.md).

See also the [current tool comparison](../README.md#comparison-with-related-tools), [verification notes](VERIFICATION.md), and [Android validation matrix](ANDROID-VALIDATION-MATRIX.md).
