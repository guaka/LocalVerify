# Why Local Verify does not embed MVT

Local Verify uses selected Amnesty and MVT indicator datasets, but does not embed the MVT analysis engine. This note explains the current architecture, not a claim that Local Verify provides better detection or replaces an investigator using MVT. Upstream references reviewed 2026-09-07.

## Different execution boundary

Local Verify's workflow is to import diagnostics, analyze them offline on the affected phone, and keep evidence there unless the user explicitly exports it. See the [privacy model](PRIVACY.md).

[MVT](https://github.com/mvt-project/mvt) is a Python-based forensic toolkit aimed at investigators using command-line tools. Adapting that runtime, dependencies and modules to both native mobile apps would be a separate integration and validation effort, not a drop-in replacement for the current engine. This is an engineering tradeoff, not a claim that running Python on phones is impossible.

MVT can analyze evidence locally on a computer; it is not inherently a cloud service. Our distinction is keeping analysis on the phone, not suggesting MVT necessarily uploads evidence.

## Sysdiagnose support is not a complete bundled detector

MVT accepts sysdiagnose archives, but its [sysdiagnose documentation](https://docs.mvt.re/en/latest/ios/sysdiagnose/) says forensic checks come from installed plugins. The built-in metadata module alone does not establish a clean analysis. Embedding MVT would still require selecting, packaging and validating appropriate checks.

## Separate code and dataset licences

Local Verify's own code is [AGPL-3.0-or-later](../LICENSE). The [MVT 1.1 licence](https://github.com/mvt-project/license/blob/main/MVT%20License%201.1.txt) adds consent-based use restrictions. Those restrictions make it a non-free licence under the [FSF's Free Software Definition](https://www.gnu.org/philosophy/free-sw.en.html): freedom 0 requires the freedom to run a program for any purpose. This applies the FSF's definition; it does not claim that the FSF has published an assessment of MVT specifically. We therefore do not treat MVT implementation code as an ordinary freely licensed dependency or assume it can be relicensed under AGPL. Any proposed incorporation needs a review of the specific revision and its terms.

Our preference for a free-software licence also reflects a practical concern: malicious actors cannot be assumed to obey licence restrictions. Such restrictions are not a technical barrier to abuse, while compliant developers and legitimate users must account for them when reusing and distributing software. This is a rationale for our licensing choice, not a claim that consent requirements have no value or that upstream terms can be ignored. We support consensual analysis and emphasize privacy-preserving defaults and explicit user control.

Dataset terms are separate: the bundled Amnesty collections use CC BY 2.0 and the selected MVT indicator collections use MIT. Their provenance and notices are recorded in [third-party notices](../THIRD_PARTY_NOTICES.md).

## The cost of this choice

Local Verify implements a narrower set of parsing and indicator checks. It does not inherit MVT's modules, forensic coverage or validation simply by reading STIX indicators. Public indicators and no-match results cannot prove a device is safe. See [verification limits](VERIFICATION.md) and the [comparison table](../README.md#comparison-with-related-tools).

MVT remains a useful methodology and comparison reference for expert-led investigations. Future integration would need to preserve the phone-local evidence boundary, satisfy applicable licences, and demonstrate coverage with synthetic acceptance tests before making stronger claims.
