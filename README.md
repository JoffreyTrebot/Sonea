# RelaxingApp - iOS Soundscape Generator

## Overview

RelaxingApp is a native iOS application designed to help users improve sleep and reduce stress by creating and playing custom relaxing soundscapes. Users can craft bespoke sound mixes tailored to their preferences, aiding relaxation and promoting better sleep.

## Features

*   **Custom Sound Mixes:** Create personalized soundscapes by combining various sounds.
*   **Multi-track Playback:** Play multiple sounds simultaneously with smooth transitions (fade-in, fade-out, crossfade).
*   **Background Audio:** Continue listening to sound mixes even when the app is in the background or the device is locked.
*   **User Accounts:** Save and manage custom sound mixes (requires Supabase integration).
*   **Sound Library:** Access a library of sounds, potentially downloadable for offline use.

## Tech Stack

*   **Platform:** iOS 17+
*   **UI Framework:** SwiftUI 4 (No Storyboards)
*   **Language:** Swift 5.9+ (Utilizing Swift Concurrency `async/await`)
*   **Audio Engine:** AVFoundation (`AVAudioEngine` for multi-track playback and `AVAudioSession` for background audio)
*   **Backend (Optional):** Supabase (using `supabase-swift` 1.x) for user accounts, saving mixes, and potentially storing sounds.
*   **Linting:** SwiftLint 0.51+

## Project Structure

The project follows the MVVM (Model-View-ViewModel) architecture pattern.

```
RelaxingApp/
├── RelaxingAppApp.swift       # @main App entry point
├── Resources/                 # Asset catalogs, bundled metadata
├── Models/                    # Data types (e.g., Sound.swift, Mix.swift)
├── ViewModels/                # Presentation logic (e.g., MixerViewModel.swift, AuthViewModel.swift)
├── Views/                     # SwiftUI Screens organized by feature (e.g., Views/Mixer/MixerView.swift)
│   ├── Auth/
│   ├── Mixer/
│   ├── UserMixes/
│   ├── DownloadedSounds/
│   ├── Settings/
│   └── TabBar/
├── Services/                  # Business logic & external interactions
│   ├── SupabaseService.swift  # Supabase client interaction
│   ├── AudioManager.swift     # AVAudioEngine management
│   └── DownloadManager.swift  # Asynchronous downloads & caching
├── Extensions/                # Swift/SwiftUI helpers
├── Utils/                     # Utility classes (e.g., FileManagerHelper.swift)
└── Constants/                 # Constant values (e.g., URLs.swift)
```

## Getting Started

1.  Clone the repository.
2.  Ensure you have Xcode installed (compatible with iOS 17 SDK).
3.  Install SwiftLint if you intend to contribute (`brew install swiftlint`).
4.  Open `RelaxingApp.xcodeproj` or `RelaxingApp.xcworkspace`.
5.  Configure Supabase credentials if backend features are implemented (likely in `SupabaseService.swift` or a configuration file).
6.  Build and run the application on a simulator or physical device running iOS 17+.

## Best Practices

*   **SwiftUI:** Views should be declarative and stateless. Use `@StateObject` for root ViewModels and `@ObservedObject` for child views. Use `NavigationStack` for navigation.
*   **MVVM:** One ViewModel per screen. Business logic resides in ViewModels or Services, not Views. Use `@Published` properties for UI binding.
*   **Swift Concurrency:** Use `async/await` for all network and file I/O operations.
*   **AVAudioEngine:** Preload audio buffers and handle audio session interruptions gracefully. Implement smooth transitions using `AVAudioMixing` ramps.
*   **File Management:** Store downloaded sounds in the `ApplicationSupport` directory and manage disk space (e.g., LRU cache eviction).
*   **Supabase:** Centralize client setup. Use `try await` with proper error handling. Fetch only necessary data.

## Contribution Guidelines

*   Adhere to the defined Project Structure and Tech Stack rules.
*   Follow SwiftLint guidelines.
*   Ensure all user-facing strings are localized (French and English).
*   Follow the specified color palette and typography (deep violet, pastel purples, bold headlines).
*   Implement background audio capabilities and smooth audio transitions as required. 