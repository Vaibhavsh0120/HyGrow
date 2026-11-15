# HyGrow code review — 31 October 2026

All work stays on main in the parent and both reviewed component repositories.

## Changes

- App reads only the configured devices/{deviceId}, uses canonical firmware
  schema 1, preserves missing values and correct ppm units, and tracks freshness,
  simulated/failing sensors and bounded current-session history. Local HTTP is
  optional, with foreground lifecycle/timeout/fallback; UDP discovery is absent.
- Removed fake sensor updates, fabricated charts, fake weather/chat answers and
  successful dummy pump commands. Firmware has no implemented pump control.
- Firebase tokens protect API/model/email/payment/order routes. Server-owned
  prices, buyer-bound captured payments and atomic order/stock transactions
  prevent price spoofing, payment reuse and repeated cancellation restocking.
  Linked inventory quantities now follow checkout/cancellation atomically.
- Email recipients and in-memory image attachments are validated; filesystem/URL
  attachments and fallback server credentials are removed. Image handling has
  bounded parsing, unique temporary paths and cleanup. Chat secrets stay server-side.
- Trusted admin claims replace editable client admin roles. Account changes clear
  private chat, telemetry, alerts and selections. Prototype rules restrict
  profiles, telemetry, orders, AI results and Storage; see FrontEnd/SECURITY.md.
- Firmware verifies cloud TLS CA chains, waits for SNTP, stops credential echoes,
  requires typed current password, serializes authenticated-client membership,
  defers cloud configuration persistence to the network task and validates sensor
  nonfinite/out-of-range values. Dashboard compressed assets match source.
- Removed stale root Expo/native project, cache/IDE files, uploaded samples,
  unused libraries and obsolete smoke scripts. Gitignores preserve examples and
  encrypted .env.age while excluding plaintext secrets and generated output.
- Expo SDK 57/native packages and lockfiles are aligned. Root is a command runner.
  Node 22 LTS, Docker and Windows/Linux verification use the same setup.
  Android native configuration is regenerated from the sole FrontEnd app config.
- Removed redundant automatic OTA publishing. Retained portable software checks,
  Linux firmware compile and manual mobile release. Failed Actions were traced to
  missing markdown-it entries in the lockfile; older OTA also used unsupported
  Node 18. The corrected lock and Node 22 resolve those identified causes.

## Verification

- Frontend: 29 tests; TypeScript; Expo SDK compatibility; web/Android/iOS JavaScript
  exports. Exports do not establish native Xcode/Gradle or physical-device behavior.
- Backend: 22 regression tests; Firebase Function: 3 regression tests.
- IoT: all 11 host/sensor/concurrency/dashboard/asset checks passed.
- Frontend clean npm ci passed after correcting the lock mismatch. The npm 10 clean-install dry run also passed; remote Windows/Linux
  verification remains to be checked after the user pushes.
- Root runner, API and Function dependency audit reached zero reported findings
  after compatible updates. The app retains 16 upstream Expo/Metro findings (3 high, 11 moderate,
  2 low; none critical). Some require incompatible changes; no claim of zero
  app advisories.
  Compatible gRPC and URI-decoder patches were explicitly applied. Do not use
  npm audit fix --force: it proposes downgrading Expo/Firebase across major versions.
- Firmware's pinned dependencies were recovered in an isolated core. Windows
  refused the compiler with Illegal System DLL Relocation; local firmware compile
  is not a pass. Linux CI is provided for a clean independent build.

## Live services and remaining release gates

Read-only Firebase inspection succeeded. Deployed Firestore rules allow broad
signed-in access to profiles, inventory and orders, and have no devices rule.
This remains a production vulnerability until replacement rules are migrated,
validated and deployed. Proposed Firestore source passed Google's remote syntax
check (no issues); full emulator attack tests and Storage rules are not yet
validated. Production rules/data were not modified by this review.

Provision immutable device owner_uid/device_auth_uid metadata and trusted admin
claims; migrate legacy profile/listing/order data as documented in SECURITY.md.
Set EXPO_PUBLIC_DEVICE_ID and a trusted HTTPS EXPO_PUBLIC_BACKEND_URL for release
builds, plus Admin SDK/service identities and provider secrets on the API host.
Use reachable LAN IPs during phone development. Do not put server keys in Expo.
The configured-host Android HTTP exception requires rebuilding when the hostname
changes. Browser HTTPS restrictions, native LAN permissions, cellular routing,
Wi-Fi isolation and DHCP still require real-device tests.

Firmware needs firmware plus LittleFS uploads after hardware validation. NVS
secrets remain plaintext; local dashboard login requires a trusted LAN. Rotate
any previously committed tunnel/provider secret; deleting code does not erase
history. No physical device was flashed, payment charged, email sent, or backend,
Function or Firebase rules deployed by this review. Automatic physical-device
compatibility across all hardware cannot be established from bundle/host tests.

Encrypted FrontEnd/.env.age and BackEnd/.env.age use the supplied public recipient;
private keys and plaintext environments remain local.

Pushing was left to the user after terminal credential-manager errors, as
requested. No alternate branch was created. Remaining local commits include the
IoT security fixes and final install/audit fixes; encrypted environments remain
tracked. No connected-app branch ref was changed during the abandoned upload.

Final Expo compatibility check additionally aligned expo-dev-client 57.0.20,
expo-image-picker 57.0.20 and expo-notifications 57.0.23 after the SDK patch
updates. The compatibility check, TypeScript and 29 app tests passed again.
README local links were checked. Final changes are local commits on main;
pushing was explicitly deferred to the user due the college network/login issue.

Final patched web, Android and iOS JavaScript exports all passed. The lockfile
records Linux x64, macOS arm64 and Windows x64 native CSS packages. This still
does not replace Xcode/Gradle and physical-device validation. The user confirmed
component-repository pushes; subsequent software changes are committed on main
as 9f5f865. The parent README, review and submodule pointers remain uncommitted
because writing the parent .git/index.lock was denied by the workspace policy.
