# Beeep 🎮

> A serverless, local peer-to-peer multiplayer iOS party game built with **SwiftUI** and Apple's **MultipeerConnectivity** framework. Developed at the **Apple Developer Academy**.

[![Swift](https://img.shields.io/badge/Swift-5.10%2B-orange.svg?style=flat&logo=swift)](https://developer.apple.com/swift/)
[![iOS](https://img.shields.io/badge/iOS-18.0%2B-blue.svg?style=flat&logo=apple)](https://developer.apple.com/ios/)
[![SwiftUI](https://img.shields.io/badge/UI-SwiftUI-red.svg?style=flat)](https://developer.apple.com/xcode/swiftui/)
[![Framework](https://img.shields.io/badge/Networking-MultipeerConnectivity-brightgreen.svg)](https://developer.apple.com/documentation/multipeerconnectivity)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## 📌 Overview

**Beeep** is an interactive party game that brings people together in the same room without needing an active internet connection or a backend server. Players connect their devices locally over Wi-Fi and Bluetooth to compete in rounds of creative wordplay, scenario responses, and anonymous voting.

One of the project's standout features is its **External Display / Second Screen support**: hosts can project the public game phases (reveals, voting tallies, and leaderboards) onto an Apple TV, external monitor, or AirPlay screen while each player interacts secretly from their individual iPhones.

---

## 🌟 Key Features & Engineering Highlights

* **Local Peer-to-Peer Mesh Networking**:
  * Powered entirely by Apple's `MultipeerConnectivity` framework (`MCNearbyServiceAdvertiser` & `MCNearbyServiceBrowser`).
  * Zero cloud dependencies: automatic local device discovery, lobby code matching, and custom binary/JSON payload serialization.
  * Real-time state synchronization ensuring all connected peers stay aligned on round phases, submissions, and vote tallies.

* **Dual-Screen Architecture (`UIWindowScene` & `UIHostingController`)**:
  * Dynamically detects external screen connections (`ScreenManager.swift`) via `UIApplication.didBecomeActiveNotification` and scene lifecycle events.
  * Automatically provisions a dedicated `UIWindow` on the external display showing public party visuals (`ExternalScreenView.swift`), creating a console-like party game experience (similar to Jackbox Games).

* **Modular SwiftUI UI/UX**:
  * Component-driven interface featuring custom text inputs, animated action buttons, and modal dialogs.
  * Multi-step stateful navigation transitions between Lobby, Submission, Reveal, Voting, and Game Over views.

* **Multi-Language Support**:
  * Fully localized in **English**, **Italian**, and **Spanish** using Apple's modern String Catalogs (`Localizable.xcstrings`).

---

## 🔄 Game Loop Architecture

```
   ┌────────────────────────────────────────────────────────┐
   │                    LOBBY SYSTEM                        │
   │   Host creates room with PIN  <───>  Peers join locally │
   └───────────────────────────┬────────────────────────────┘
                               │
                               ▼
   ┌────────────────────────────────────────────────────────┐
   │               PHASE 1: WORD SUBMISSION                 │
   │  Each player submits a word + definition secretly      │
   └───────────────────────────┬────────────────────────────┘
                               │
                               ▼
   ┌────────────────────────────────────────────────────────┐
   │             PHASE 2: SCENARIO & SENTENCE               │
   │  A prompt is revealed; players write witty responses   │
   └───────────────────────────┬────────────────────────────┘
                               │
                               ▼
   ┌────────────────────────────────────────────────────────┐
   │               PHASE 3: ANONYMOUS VOTING                │
   │  Submissions displayed on public/external display;     │
   │  players vote on their phones                          │
   └───────────────────────────┬────────────────────────────┘
                               │
                               ▼
   ┌────────────────────────────────────────────────────────┐
   │             PHASE 4: RESULTS & LEADERBOARD             │
   │  Points awarded, winner celebrated, new round starts   │
   └────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```text
Beeep/
├── ParolacciappersProject/
│   ├── Component/              # Reusable UI components & Design System
│   │   ├── ActionButton.swift
│   │   ├── CustomAlert.swift
│   │   ├── CustomTextField.swift
│   │   ├── Localizable.xcstrings   # EN / IT / ES String Catalog
│   │   └── ...
│   ├── Views/                  # Game phase screens & flow
│   │   ├── LobbyView.swift
│   │   ├── HostLobbyView.swift
│   │   ├── JoinLobbyView.swift
│   │   ├── GameView.swift
│   │   ├── SentenceSubmissionView.swift
│   │   ├── VotingView.swift
│   │   ├── ExternalScreenView.swift # Dedicated TV/Monitor scene
│   │   └── ...
│   ├── MultipeerConnectionManager.swift # Peer-to-peer networking core
│   ├── ScreenManager.swift              # Dual-screen & external display controller
│   ├── ParolacciappersProjectApp.swift  # SwiftUI App Entrypoint
│   └── Info.plist                       # Bonjour service declarations
└── ParolacciappersProject.xcodeproj     # Xcode Project configuration
```

---

## 🛠️ Project Status & Roadmap

> [!NOTE]
> This project is currently maintained as a **Functional Prototype** developed during the Apple Developer Academy. The core multiplayer engine, multi-screen projection, and round cycles are implemented.

### Current Implementation:
- [x] Multipeer discovery, lobby creation, and PIN-code joining.
- [x] Full multiplayer game cycle (Word Submission ➔ Reveal ➔ Sentence ➔ Voting ➔ Winner calculation).
- [x] External monitor detection and view hosting.
- [x] Reusable component library and multi-language support.

### Planned Enhancements / Roadmap:
- [ ] Automatic peer reconnection and packet loss recovery on Wi-Fi drops.
- [ ] Sound design and custom haptic feedback (`CoreHaptics`).
- [ ] Configurable round timers and custom scenario pack editor.
- [ ] Architecture refactor to modern `Observation` (`@Observable`) and Swift Concurrency (`async/await`).

---

## 🚀 Getting Started

### Prerequisites
* Mac running macOS Sonoma / Sequoia
* Xcode 16.0 or higher
* iOS 18.0+ deployment target
* 2 or more physical iOS devices (or Simulator + Physical Device) connected to the same Wi-Fi / Bluetooth network

### Setup & Run
1. Clone the repository:
   ```bash
   git clone https://github.com/vincenzopiconee/Beeep.git
   cd Beeep
   ```
2. Open the project in Xcode:
   ```bash
   open ParolacciappersProject.xcodeproj
   ```
3. Ensure your development team is selected under **Signing & Capabilities**.
4. Select your target device and hit **Run** (`⌘R`).

> **Note on Bonjour Services:** Local networking requires Bonjour discovery permissions defined in `Info.plist`:
> ```xml
> <key>NSBonjourServices</key>
> <array>
>     <string>_game-lobby2._tcp</string>
>     <string>_game-lobby2._udp</string>
> </array>
> ```

---

## 👨‍💻 Author

**Vincenzo Picone**  
Apple Developer Academy Graduate  
* GitHub: [@vincenzopiconee](https://github.com/vincenzopiconee)

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
