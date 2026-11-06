# Beeep

> A serverless, local peer-to-peer multiplayer iOS party game built with SwiftUI and Apple's MultipeerConnectivity framework. Developed at the Apple Developer Academy.

[![Swift](https://img.shields.io/badge/Swift-5.10%2B-orange.svg?style=flat&logo=swift)](https://developer.apple.com/swift/)
[![iOS](https://img.shields.io/badge/iOS-18.0%2B-blue.svg?style=flat&logo=apple)](https://developer.apple.com/ios/)
[![SwiftUI](https://img.shields.io/badge/UI-SwiftUI-red.svg?style=flat)](https://developer.apple.com/xcode/swiftui/)
[![Framework](https://img.shields.io/badge/Networking-MultipeerConnectivity-brightgreen.svg)](https://developer.apple.com/documentation/multipeerconnectivity)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## Overview

Beeep is a local multiplayer iOS party game engineered to operate entirely without internet connectivity or central cloud infrastructure. Utilizing local Wi-Fi and Bluetooth mesh networking, players connect directly between devices to participate in timed rounds of wordplay, scenario prompt reactions, and anonymous peer voting.

A core technical highlight is the integrated **Dual-Screen Architecture**: the host device can drive an external secondary monitor (via AirPlay, HDMI, or USB-C DisplayPort) to display public round transitions, active prompts, and voting tallies, while individual player devices act as private controllers for confidential submissions.

---

## Architecture and Engineering Highlights

### 1. Peer-to-Peer Mesh Networking (`MultipeerConnectivity`)
* **Decentralized Topology**: Bypasses traditional client-server overhead by establishing an ad-hoc local mesh using Apple's `MultipeerConnectivity` framework.
* **Service Discovery**: Implements `MCNearbyServiceAdvertiser` and `MCNearbyServiceBrowser` under custom Bonjour identifiers (`_game-lobby2._tcp`, `_game-lobby2._udp`).
* **Session Lifecycle**: Coordinates peer handshake, dynamic connection validation, lobby PIN-code verification, and multi-peer state broadcasting.
* **Event Dispatching**: Centralized in `MultipeerManager.swift` via the `ObservableObject` pattern, synchronizing game phase enums, payload packets, and player scores in real time.

### 2. Dual-Screen External Display Management (`ScreenManager`)
* **Window Scene Observation**: Leverages UIKit interoperability through `UIWindowScene` and scene connection notifications (`UIApplication.didBecomeActiveNotification`).
* **Independent Public View Hierarchy**: When an external screen is detected (`screen != UIScreen.main`), `ScreenManager` dynamically constructs a dedicated `UIWindow` hosting `ExternalScreenView`.
* **Privacy Isolation**: Keeps private input interactions (text submissions, secret voting choices) isolated to player handsets while projecting synchronized public game states onto the shared display.

### 3. Modular SwiftUI Component Architecture
* **Atomic Design Structure**: Custom reusable interface elements (`ActionButton`, `CustomTextField`, `CustomAlert`, `AddPhotoButton`) built for adaptability and uniform layout rhythm.
* **State-Driven Navigation**: Uses `NavigationStack` bound to published networking state flags, ensuring deterministic screen transitions across all connected devices.

### 4. Internationalization and Localization
* **Apple String Catalogs**: Implemented via `Localizable.xcstrings`, supporting English (`en`), Italian (`it`), and Latin American Spanish (`es-419`).

---

## Game Loop Architecture

```
   +--------------------------------------------------------+
   |                    LOBBY SYSTEM                        |
   |   Host generates 4-digit code <-> Local peers discover  |
   +---------------------------+----------------------------+
                               |
                               v
   +--------------------------------------------------------+
   |               PHASE 1: WORD SUBMISSION                 |
   |  Each peer submits a confidential word and definition  |
   +---------------------------+----------------------------+
                               |
                               v
   +--------------------------------------------------------+
   |             PHASE 2: SCENARIO & SENTENCE               |
   |  A prompt is selected; players write context responses |
   +---------------------------+----------------------------+
                               |
                               v
   +--------------------------------------------------------+
   |               PHASE 3: ANONYMOUS VOTING                |
   |  Submissions projected publicly; votes cast on phones  |
   +---------------------------+----------------------------+
                               |
                               v
   +--------------------------------------------------------+
   |             PHASE 4: RESULTS & LEADERBOARD             |
   |  Vote aggregation, score computation, winner declared   |
   +--------------------------------------------------------+
```

---

## Project Structure

```text
Beeep/
├── ParolacciappersProject/
│   ├── Component/                        # Reusable design system & UI components
│   │   ├── ActionButton.swift            # Standardized primary interaction button
│   │   ├── CustomAlert.swift             # Modal dialog container
│   │   ├── CustomTextField.swift         # Styled text entry field
│   │   ├── Localizable.xcstrings         # Multi-language string catalogs
│   │   └── ...
│   ├── Views/                            # Game phase views & navigation flow
│   │   ├── LobbyView.swift               # Role selection (Host vs. Join)
│   │   ├── HostLobbyView.swift           # Host room management & peer roster
│   │   ├── JoinLobbyView.swift           # Discovered lobbies list & PIN entry
│   │   ├── GameView.swift                # Master game coordinator container
│   │   ├── SentenceSubmissionView.swift  # Timed response entry screen
│   │   ├── VotingView.swift              # Anonymous ballot submission interface
│   │   ├── ExternalScreenView.swift      # Public presentation scene for TVs/monitors
│   │   └── ...
│   ├── MultipeerConnectionManager.swift   # Core P2P networking & state synchronization
│   ├── ScreenManager.swift                # External display detection & scene controller
│   ├── ParolacciappersProjectApp.swift    # App lifecycle entrypoint
│   └── Info.plist                         # Bonjour service declarations
└── ParolacciappersProject.xcodeproj       # Xcode build configuration & schemes
```

---

## Project Status and Technical Roadmap

This repository reflects a **Functional Prototype** produced during the Apple Developer Academy curriculum. The core local networking protocol, multi-display projection pipeline, and core game phases are implemented and operable.

### Implemented Capabilities
- [x] Local peer discovery, room advertising, and PIN-protected lobby connection.
- [x] End-to-end game lifecycle: Word Submission -> Reveal -> Scenario -> Voting -> Results.
- [x] Automated external screen detection and `UIWindowScene` projection.
- [x] Reusable component architecture and trilingual localization (`.xcstrings`).

### Technical Roadmap
- [ ] **Network Resiliency**: Implement reconnect tokens and packet queueing to seamlessly recover from temporary signal degradation.
- [ ] **Modern Swift Concurrency**: Migrate delegate-based and completion-driven network pipelines to `async`/`await` and Swift 6 structured concurrency.
- [ ] **Observation Framework Migration**: Transition data classes from `ObservableObject` / `@Published` to `@Observable` (iOS 17+).
- [ ] **Haptics and Audio**: Integrate `CoreHaptics` and `AVFoundation` sound triggers for round phase transitions and countdown urgency.

---

## Getting Started

### Prerequisites
* macOS Sonoma 14.0 or macOS Sequoia 15.0+
* Xcode 16.0 or higher
* iOS 18.0+ deployment target
* Minimum of 2 physical iOS devices (or 1 physical device and 1 simulator on the same local subnet / Bluetooth range)

### Installation and Execution
1. Clone the repository:
   ```bash
   git clone https://github.com/vincenzopiconee/Beeep.git
   cd Beeep
   ```
2. Open the project in Xcode:
   ```bash
   open ParolacciappersProject.xcodeproj
   ```
3. In Xcode, navigate to the target settings under **Signing & Capabilities** and set your Development Team.
4. Build and deploy to your testing devices (`Cmd + R`).

### Bonjour Network Permissions
Local discovery relies on Bonjour service declarations in `Info.plist`:
```xml
<key>NSBonjourServices</key>
<array>
    <string>_game-lobby2._tcp</string>
    <string>_game-lobby2._udp</string>
</array>
```

---

## Contributing and Development Guidelines

Contributions, bug reports, and architectural proposals are welcome. To maintain code hygiene and project consistency, please observe the following workflow:

### Branching Strategy
* `main`: Stable, release-ready prototype branch.
* `feature/<feature-name>`: Dedicated branches for new game phases, UI modules, or network enhancements.
* `fix/<bug-description>`: Targeted bug fixes.

### Development Workflow
1. **Fork the Repository**: Create a personal fork on GitHub.
2. **Create a Feature Branch**:
   ```bash
   git checkout -b feature/audio-feedback-manager
   ```
3. **Coding Standards**:
   * Adhere to [Apple's Swift API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/).
   * Keep views decoupled: business and network logic should remain in managers or view models rather than inline within SwiftUI `View` bodies.
   * Prefer reusable components from `Component/` over one-off custom modifiers.
   * Ensure any newly introduced user-facing text is registered in `Localizable.xcstrings`.
4. **Testing Multipeer Networking**:
   * Because `MultipeerConnectivity` relies on Bluetooth/Wi-Fi hardware, verify new networking logic on at least two physical hardware devices before opening a Pull Request.
5. **Submitting a Pull Request**:
   * Provide a concise description of the problem solved or feature added.
   * Include device models and iOS versions used during verification.

---

## Author

**Vincenzo Picone**  
Apple Developer Academy Graduate  
GitHub: [@vincenzopiconee](https://github.com/vincenzopiconee)

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for complete terms.
