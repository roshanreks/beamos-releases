# Automatic wall scan — implementation and verification ledger

The owner's target is an automatic, no-corner-tapping scan on an iPhone 15 Pro Max, with the Mac/Chrome remote watching the same camera feedback. The projection should fit measured clear wall space, remain 16:9, save after verification, and survive disconnects. Manual focus remains necessary.

## Current boundary (20 October 2026)

Studio 0.20 is built and locally installed. It adds an opaque white 16×9 scan grid with four versioned QR markers, temporary lease-owned camera previews, fast expiry rollback, verified durable LiDAR fits and shared browser camera viewing with target/exclusion overlays. Photo fitting from 0.18/0.19 remains available. There has been **no physical phone camera calibration run** and no new automatic geometry was applied during testing.

The native companion now has SwiftUI screens, pairing/Keychain storage, a restricted local web remote, ARKit capture, Vision QR detection, depth-based wall/obstacle scanning, target selection and a bounded feedback loop. The iOS source passes parsing; the independent algorithm library compiles. **The app has not been built against an iPhone SDK, signed, installed or tested on the phone.** Do not present it as an available working IPA. The missing full Xcode/iOS SDK is the next blocker.

Apple Developer sign-in was verified. The official Xcode 26.6 Apple silicon download (2.16 GB) was found under More Downloads. This compatible version avoids upgrading macOS 26.3.2. Chrome returned ERR_BLOCKED_BY_CLIENT when the download was triggered; the owner has been asked to click that official download directly. No macOS upgrade or new Apple agreement was accepted. A full Xcode installation may subsequently need the owner to accept Apple's license and configure device signing.

Public publishing is separate from local installation: the public signed feed was last verified at 0.18. A local 0.19 publication commit remains ahead of origin. GitHub HTTPS and SSH connections from this Mac time out; the connected GitHub app reports read-only permissions for this repository. Do not claim 0.19/0.20 is publicly deployed until the signed public feed and APK hashes are rechecked.

## Measurement and feedback

1. Show the dedicated `scan` guide. It covers the video picture briefly; it does not intentionally pause audio.
2. Retain only the latest AR frame. Use scene depth and confidence, normal camera tracking and vertical AR wall anchors.
3. Decode all four `BEAM1:<corner>:<16 base36 inset digits>:<width>x<height>` QR markers. Reject mixed, duplicate or stale geometry. Their centres are mapped through the active keystone before reconstructing the optical panel-to-wall transform.
4. Use a gravity-aligned wall frame in metres. High-confidence foreground depth and dark regions that should be white in the scan pattern exclude space. Unknown cells are blocked, with clearance around measured obstructions. This is measured exclusion, not semantic recognition of every object.
5. Choose a large axis-aligned 16:9 rectangle within measured free cells, the optical footprint and firmware corner limits. Keep the target stable; reselect after a persistent obstruction.
6. Apply damped steps of at most 20 per-mille per corner value. Wait for hardware readback and at least 300 ms of settling before considering the next measurement. Stop on stale camera data, conflicts, non-convergence, loss of coverage or timeout.
7. Both phone and controller require five fresh stable measurements spanning at least two seconds: four markers, estimated max corner error ≤5 mm, estimated aspect error ≤1%, stable wall/phone, checked clear space and matching current geometry. Green is an estimated camera tolerance, **not a proven absolute physical accuracy**.
8. Save the fit and metre-based transform. Shrink/grow proportionally within its verified area. Going beyond that area requires a new scan. Remove the grid after saving; the green screen in the native app then describes the last verified scan, not ongoing background monitoring.

The companion must remain in the foreground. Another browser or a Mac can view the feed at the same time. Switching the iPhone into Chrome backgrounds the native camera and stops its scan; browsers do not receive ARKit/LiDAR measurements through this HTTP remote.

## Ownership, persistence and privacy

There is one short-lived publisher and multiple paired viewers. A monotonically increasing frame sequence prevents replay. A frame must be less than 750 ms old when sent; controller frames expire after 2.5 seconds. A 500 ms watchdog conditionally cancels only the camera's exact temporary preview generation, including protection against a later manual edit with identical coordinates. A replacement publisher cannot be stopped by the old publisher's lease. A confirmed fit is never reverted by camera expiry.

Frames are downscaled on the phone to 768 px, sent as bounded JPEGs and held in controller RAM. Viewer frame requests carry authentication in headers and select the sequence they observed. Images and overlays are cleared on expiry, loss of connection and hidden browser tabs. No photos are uploaded to a cloud analysis service. Pairing secrets stay in the iPhone Keychain and are injected only into the exact local remote origin. Native API redirects are not followed.

A sudden controller process/power loss still falls back to the adapter's existing 120-second temporary-preview timer; the fast watchdog runs only while Studio is alive. Persistent confirmed geometry continues to use the adapter's durable path.

## Verification already run

- 53 isolated Android persistence/ownership/resize checks using a fake adapter: no live wall geometry changes.
- 28 camera publisher/gate checks with real JPEG decoding and an injected monotonic clock.
- 52 compiled Swift geometry, marker, wall-ray, conservative occupancy, tolerance, bounded-correction and feedback assertions.
- 9 shared-camera viewer checks: serialized polls, header auth, frame sequence matching, overlays, expiry, hidden tabs and failure states.
- Photo path: 10 geometry/actual QR raster checks, 9 save-transaction checks; original HEIC decoding was verified earlier in Chrome.
- Existing regressions: 12 picture UI checks, 10 mapping persistence checks and 7 input-focus routing checks.
- Android signed release build, Swift source parsing, generated Xcode project/plist syntax validation.

These checks do not establish phone SDK type correctness, camera/depth performance, real obstacle detection, optical accuracy or physical wall convergence.

## Next physical acceptance session

Install compatible Xcode and its iOS platform, build the included project without signing first, fix all SDK/compiler diagnostics, then configure the owner's signing team and connected iPhone. Pair from the six-digit projector code. Keep the entire wall projection in view and verify missing/obstructed QR codes remain amber. Test a known flat wall, a nearby obstruction and a moving person; measure final width/height and both diagonals independently. Test phone movement, network loss, backgrounding, manual edits during scanning, cancellation, power loss and restart. Tune tolerances and throughput from actual results. Do not enable unattended correction until this session passes.

A single oblique photograph without a measured planar reference, depth or another metric cue cannot determine a physically correct wall aspect ratio. The existing photo-only fit is in camera view; the measured reference workflow remains an advanced fallback. Automatic image-quality/contrast tuning needs a separate controlled-exposure capture protocol and validation; a phone camera is not a colourimeter.

## Other outstanding work retained

Official TV-friendly Netflix installation/certification is unresolved; no DRM licenses were installed or bypassed. Ordinary Android text and split OTP input were device-tested, but YouTube's custom search keyboard may not expose a standard text connection. Keep investigating that independently. No LED-current or thermal safety limit was bypassed, and no unsupported fan command was added.
