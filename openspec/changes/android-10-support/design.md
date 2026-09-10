## Context

See proposal.md for motivation. The app currently declares minSdk 30 and compile/target SDK 34. MainActivity already guards the API 31 overlay-hiding call. Imports use the Storage Access Framework and private app storage. Existing SyntheticWorkflowTest generates archives inside an emulator.

## Goals / Non-Goals

**Goals:** Extend installation eligibility to API 29 and check existing workflows for compatibility.

**Non-Goals:** Change target SDK, engine behavior, iOS, or experimental/shared harness minimums.

## Decisions

Lower only the production minimum SDK to 29. Lowering target/compile SDK would change unrelated behavior and is unnecessary. Retain version guards and existing privacy settings. Add installed-package minimum SDK and backup-policy assertions to the existing synthetic workflow suite, and document API 29 plus newer-emulator execution.

## Risks / Trade-offs

- Calls introduced after API 29 could fail at runtime → run lint with the lowered minimum and exercise synthetic instrumentation workflows.
- This host currently has only an API 36 emulator image → record API 29 runtime testing as pending if a disposable API 29 emulator is unavailable; do not claim runtime validation or archive until verified.

## Migration Plan

Build and distribute the APK through the existing process. No data migration is required. Reverting minSdk to 30 restores the previous installation floor.
