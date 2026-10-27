---
title: "Week 5 Worklog"
date: 2026-10-18
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---


### Week 5 Objectives:

* Understand the purpose and boundaries of Encrypted Media Extensions (EME), including the browser, Content Decryption Module (CDM), key system, application, and license/key server roles.
* Learn how Shaka Player connects an encrypted manifest to a configured key system and license endpoint, and identify the settings required for a valid playback environment.
* Understand how Shaka Packager encrypts media and signals protection information in DASH/HLS outputs, then outline a small, testable DRM integration workflow for SecureLearn.

### Tasks to be carried out this week:
| Day | Task / Activity Description | Start Date | Completion Date | Resource Link |
| --- | --- | --- | --- | --- |
| Mon | - Study the EME overview and its purpose.<br>- Distinguish EME (a browser API) from a complete DRM system or license server.<br>- Identify the roles of the web application, browser/CDM, key system, encrypted media, and license service.<br>- Note that application-level authentication and authorization are not provided automatically by EME.<br>- Output: a high-level DRM playback flow and glossary. | 12/10/2026 | 12/10/2026 | - W3C: Encrypted Media Extensions<br>- Shaka Player: DRM Configuration |
| Tue | - Trace the EME lifecycle: requestMediaKeySystemAccess(), MediaKeys, and MediaKeySession.<br>- Understand how initialization data triggers generateRequest() and a message event.<br>- Map the license exchange: application sends the message to a service, receives a response, then passes it to session.update().<br>- Review key-status events and the difference between temporary and persistent-license sessions.<br>- Output: a sequence diagram of the browser-to-license-service exchange. | 13/10/2026 | 13/10/2026 | - W3C: Encrypted Media Extensions |
| Wed | - Study player.configure() and the drm.servers mapping between key-system IDs and license-server URLs.<br>- Review Clear Key configuration for controlled testing and diagnosis; understand that it is not equivalent to production-grade commercial DRM.<br>- Inspect drm.advanced options such as license-request headers and key-system robustness requirements.<br>- Check environment requirements: use HTTPS or localhost for EME, and keep the manifest and segments on HTTPS when the page uses HTTPS.<br>- Output: a Shaka Player DRM configuration checklist. | 14/10/2026 | 14/10/2026 | - Shaka Player: DRM Configuration<br>- W3C: Encrypted Media Extensions |
| Thu | - Study Shaka Packager key-source options: raw keys, a supported key server, and CPIX.<br>- Understand key ID (KID), content key, IV, and PSSH/protection signaling, and how the packager writes protection information into output manifests.<br>- Compare supported protection schemes such as cenc and cbcs, and review the protection_systems option.<br>- Review clear-lead and key-rotation settings; keep raw keys and test IVs restricted to a controlled test environment.<br>- Output: a packaging-parameter checklist and a documented test-key approach. | 15/10/2026 | 15/10/2026 | - Shaka Packager: DRM |
| Fri | - Combine the concepts into one flow: source media -> Shaka Packager -> encrypted segments and manifest -> Shaka Player/EME -> key or license response -> playback.<br>- Define basic checks: clear playback, protected playback with matching test keys, wrong key/KID, incorrect endpoint, unsupported key system, and insecure/mixed-content URLs.<br>- Separate a local Clear Key demonstration from a real Widevine/PlayReady deployment, which requires compatible key-system/license infrastructure and appropriate access.<br>- Consolidate notes, identify integration dependencies, and record open questions for the next development stage.<br>- Output: an end-to-end diagram and a small test matrix. | 16/10/2026 | 16/10/2026 | - W3C: Encrypted Media Extensions<br>- Shaka Player: DRM Configuration<br>- Shaka Packager: DRM |


### Week 5 Achievements:

* Explained the role of EME as the interface between a web application and supported browser key systems, rather than a standalone DRM or license-server implementation.
* Documented the main license-request lifecycle: key-system selection, session creation, message delivery to the service, response processing, and key-status monitoring.
* Identified the core Shaka Player settings for protected playback, including drm.servers, optional advanced configuration, and the conditions required by secure browser contexts.
* Summarized Shaka Packager responsibilities and key concepts, including raw-key/key-server/CPIX inputs, KID and content-key association, protection schemes, manifest signaling, and key rotation.
* Prepared a practical integration outline for a controlled demo, with Clear Key/raw-key testing kept separate from production DRM expectations and credentials.
* Created a proposed test checklist covering successful playback, key/manifest mismatch, license endpoint failures, browser support, and secure URL requirements.