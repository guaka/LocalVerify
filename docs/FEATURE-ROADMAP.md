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
| Guided non-technical review and next steps | 🟡 Guidance and explanations; usability unvalidated | 🟡 Guidance and explanations; usability unvalidated | — Equivalent usability not evaluated | **First iOS release:** test with non-technical users |
| Related-match grouping | ⚪ | ⚪ | — Equivalent grouping not verified | **First iOS release:** preserve all underlying evidence |
| Reduced-detail sharing summary | ⚪ | ⚪ | — Equivalent privacy guarantees not verified | **First iOS release:** allowlisted fields; no raw evidence |
| Redacted structured evidence package | ⚪ | ⚪ | — Equivalent privacy guarantees not verified | Later iOS, then Android; parser-aware transformations |
| Whole-archive sanitization | ⚪ | ⚪ | — Reliable anonymization not verified | Research only; no anonymity guarantee |
| Archive hashing and safety limits | 🟢 | 🟢 | — Equivalent safeguards not audited | Preserve; test every new parser |
| Text and JSON indicator matching | 🟢 Limited patterns | 🟢 Limited patterns | — Exact matching semantics not compared | Retain alongside new checks |
| Import local STIX indicators | 🟢 | 🟢 | — No additional free-software equivalent verified here | Retain |
| Structured crash analysis | 🟡 Generic JSON/text matching | 🟡 Generic matching | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP parsers](https://github.com/IsMyPhonePwned) | **First iOS release** |
| Process inventory | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP parsers](https://github.com/IsMyPhonePwned) | **First iOS release:** recognized text records |
| Network observations | 🟡 Indicator hits only | 🟡 Indicator hits only | [IMPP parsers](https://github.com/IsMyPhonePwned) | **First iOS release:** supported text sources |
| App inventory and permission analysis | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP Android parser](https://github.com/IsMyPhonePwned/bugreport-extractor-library) | Later; include installation history |
| Curated diagnostic detection rules beyond STIX | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose), [IMPP sysdiagnose parser](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) | **First iOS release:** bundled, versioned checks |
| Sigma YAML rule import/evaluation | ⚪ | ⚪ | [IMPP sysdiagnose parser](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) | Later; explicit supported subset |
| Searchable event timeline | ⚪ | ⚪ | [SAF](https://github.com/EC-DIGIT-CSIRC/sysdiagnose): timeline generation | After guided-review acceptance; supported events only |
| Dedicated process/network/crash views | ⚪ | ⚪ | IMPP parsers supply structured data; web UI licence unverified | After guided-review acceptance; under Explore evidence |
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
3. **Guided review first:** deliver clear result summaries, related-match grouping, source-evidence access and practical next steps alongside the new parsers. Defer full inventories and the searchable timeline until the guided experience passes usability acceptance; put those later views under Explore evidence, not new top-level tabs. Follow [Apple's Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/). No-match results must never be presented as proof of safety.
4. **Persistence and exports:** retain structured results and rule snapshots privately and preserve readability of existing cases. Add a reduced-detail sharing summary built from an explicit field allowlist; distinguish it from existing unredacted reports and original-evidence exports. Structured redacted packages follow later as parser coverage grows.

## Usability for non-technical users

More forensic capability must not require more technical decisions in the default workflow. Keep the main journey **Import → Analyze → Review → Decide what to do next**. Use progressive disclosure within each case rather than requiring users to choose an expert mode.

The result must answer four questions: did analysis finish, did anything need attention, what does it mean (including alternative explanations), and what should I do next? Present analysis completeness separately from findings. Do not add an overall security score, a green “safe” verdict, or warnings based solely on an unfamiliar process, ordinary crash or permission grant.

| Capability | Default experience | Detail on demand |
| --- | --- | --- |
| More parsers and rules | Run supported parsers and bundled checks automatically | Versions, sources, unsupported formats and failures |
| Findings | Group related matches into review items; separate observations from suspicious findings | Every original match and supporting record |
| Process/app/network/crash records | Relevant context within a finding | Searchable inventories under Explore evidence |
| Timeline | Related events for the selected finding when available | Full searchable timeline after usability acceptance |
| Coverage | Explain what could not be checked and why it matters | Per-parser/file coverage; distinguish missing evidence from failed analysis |
| Definitions | Explain the age and limits of the definitions used | Publisher metadata and local STIX import |
| Sharing | Prepare a report for someone helping you; preview contents and sensitivity | Redaction limitations and explicit original-evidence selection |

Each finding should explain **what we found**, **what it means**, **what else could explain it**, and **what to do next**, followed by **View supporting evidence**. Grouping must not discard findings, change their identity, or make filters omit evidence from a full report. Preserve uncertainty: a record containing a published spyware indicator is a lead, not proof of infection.

Offer proportionate next steps such as preserving the report and seeking a trusted digital-security specialist. Do not default to destructive remediation or uploading evidence. Help references must respect the existing offline policy; sharing always remains an explicit action.

### Usability acceptance

Test with non-technical participants using synthetic no-match, ambiguous-finding and incomplete-analysis cases. Before expanding the dashboard, verify that participants can:

- Complete import using the collection guide without knowing what sysdiagnose means in advance.
- Distinguish “nothing matched” from “nothing is wrong,” and identify incomplete or limited analysis.
- Explain a finding's uncertainty and identify a next step without reading raw logs.
- Find supporting evidence without needing it to understand the primary result.
- Prepare the intended report without accidentally sharing the original archive, and understand the limits of redaction.
- Complete the journey with VoiceOver and larger text; meaning must not depend on color alone. Apply equivalent accessibility expectations when expanding Android.

Record participant misunderstandings and revise the wording/navigation before treating the guided experience as ready. Automated UI tests supplement, but do not replace, these checks.

## Privacy-reduced sharing

All preparation happens offline on the phone. Analyze the untouched original first; generate a separate derivative for sharing without changing the original or the saved analysis. Current exports are **not redacted**: finding values, excerpts and paths can contain private information, and the original archive can be included explicitly.

Use “reduced-detail” or “redacted,” never “anonymous.” Apple [warns that sysdiagnose may contain private information](https://developer.apple.com/forums/thread/739560). Removing direct identifiers does not prevent identification through activity patterns, timestamps, app combinations or the findings themselves.

| Export choice | Contents and defaults | Intended use |
| --- | --- | --- |
| Reduced-detail summary | Allowlisted status, finding summaries, rule versions and coverage; omit raw excerpts, matched values, source paths, exact event times, original filenames/hashes and persistent case/device identifiers | Initial request for help; first iOS milestone |
| Redacted evidence package | Selected, understood structured records with sensitive fields removed or pseudonymized; no original archive, raw fallback files or replacement mapping | Detailed review; later milestone |
| Unredacted report / original evidence | Clearly separate existing report export; original archive remains an explicit opt-in | Trusted specialist; warn before sharing |

Provide a plain-language contents preview and explain that even reduced-detail summaries can disclose sensitive findings. Choosing a redacted export must never silently include an unredacted report or original. No export is advertised as safe for public posting.

### Structured redaction approach

- Remove secrets such as tokens, cookies and credentials entirely. Omit locations by default.
- Remove or replace names, contact details, device identifiers, Wi-Fi names and addresses with labels consistent within one export. Generate a fresh mapping for each export; never include it in the shared package. Do not use unkeyed identifier hashes as an anonymity measure.
- Use relative times by default where event ordering is useful; retain unknown timestamps as unknown. Any later date-shifting option must document its effect on interpretation.
- Strip URL credentials, query strings and fragments; transform sensitive path components. Treat remaining hosts and paths as potentially identifying, not automatically safe.
- Apply field-aware policies to supported records. Exclude unknown formats, unparsed content and unrecognized fields rather than copying them through. Regex replacement alone is not sufficient for whole-archive sanitization.
- Record the redaction-policy version, transformed/omitted categories and resulting analytical limitations in a sanitized manifest. Do not put removed values or identifying source paths back into that manifest, filenames, errors or archive metadata.
- A matched domain or process may be essential to verify a finding. Report when redaction prevents independent reproduction; do not claim that the derivative produces equivalent detection results. Preserve the original-to-derivative association privately on the phone, not through an identifying original hash in the reduced-detail package.

Whole-archive sanitization remains a separate research track. It must not become an “anonymous export” checkbox; unsupported binary content and residual re-identification risks prevent that promise.

## Verification and boundaries

- Test synthetic positive, negative, malformed, oversized, duplicate and interrupted-analysis cases.
- Verify timestamp handling, coverage reporting, historical-case compatibility, escaped exports and operation with networking unavailable.
- For privacy-reduced exports, plant synthetic identifiers/secrets in fields, excerpts, paths, filenames, errors and metadata; assert their absence from every exported entry. Verify unknown content is excluded, mappings are consistent within an export and fresh across exports, originals remain byte-identical, and cancellation/failure cannot expose a partial unredacted package.
- Test the sharing choices with non-technical users using synthetic cases: they must understand what leaves the phone, distinguish redacted from original evidence, and recognize that redaction is not guaranteed anonymity.
- Never transfer actual phone evidence for development without explicit permission; follow [AGENTS.md](../AGENTS.md).
- Create OpenSpec scenarios, design and tasks before implementation; mark features implemented only after integration and verification.
- Keep the existing shared-engine migration separate until an implementation approach is selected. See the [engine contract guide](ENGINE-EXPERIMENT.md).
- Treat broader coverage as an aspiration, not an unconditional promise: phone sandboxing, available evidence, resource limits and licensing constrain feasibility.
- “IMPP parsers” refers to the Apache-2.0 [sysdiagnose](https://github.com/IsMyPhonePwned/sysdiagnose-extractor-library) and [bugreport](https://github.com/IsMyPhonePwned/bugreport-extractor-library) components, not blanket approval of the entire IsMyPhonePwned project. Review each dependency and rule collection before reuse; component licensing does not establish the licence of the web UI or every rule.
- MVT and AndroidQF remain methodology references, but are excluded from the free-software column under the FSF definition discussed in the [licensing rationale](WHY-NOT-MVT.md).

See also the [current tool comparison](../README.md#comparison-with-related-tools), [verification notes](VERIFICATION.md), and [Android validation matrix](ANDROID-VALIDATION-MATRIX.md).
