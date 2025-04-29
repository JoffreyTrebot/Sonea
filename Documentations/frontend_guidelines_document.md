# Frontend Guideline Document

Welcome to the Relaxing App frontend guide. This document covers the overall architecture, design principles, styling, component structure, state management, navigation, performance, testing, error handling, security, and analytics. By the end, you’ll have a clear understanding of how the app is built and maintained, even without a deep technical background.

## Frontend Architecture

**Frameworks and Libraries**
- **Language & UI:** Swift with SwiftUI for all screens and controls.
- **Architecture Pattern:** MVVM (Model-View-ViewModel) separates UI (View) from business logic (ViewModel) and data (Model).
- **Audio Engine:** AVAudioEngine for mixing multiple sound tracks, smooth fades, and crossfades.
- **Concurrency:** Swift Concurrency (async/await) for network calls, file I/O, and non-blocking UI updates.
- **Local Storage:** FileManager to download and store audio files for offline use.
- **Backend Integration:** Supabase Swift client for authentication, database reads/writes, and fetching/storing audio files.

**Scalability, Maintainability, Performance**
- MVVM makes it easy to add new features by creating new ViewModels and views without touching existing code.
- SwiftUI’s declarative syntax ensures UI updates automatically when underlying data changes.
- Async/await prevents UI hangs by offloading I/O and network tasks.
- Local caching of audio reduces network usage and speeds up playback.

## Design Principles

1. **Usability**
   - Simple, intuitive mixer controls and one-tap save/play for custom soundscapes.
   - Clear icons and labels for volume sliders, download status, and timer settings.

2. **Accessibility**
   - Support for VoiceOver, Dynamic Type (font scaling), and high-contrast modes.
   - Meaningful accessibility labels on buttons and sliders.

3. **Responsiveness**
   - SwiftUI layouts adapt to all iPhone screen sizes and orientations.
   - Light and Dark mode support out of the box.

4. **Consistency & Feedback**
   - Uniform button styles, spacing, and animations.
   - Immediate visual feedback on user actions (e.g., button press, volume change).

## Styling and Theming

**Overall Style:** Modern and dreamy with subtle glassmorphism elements (translucent cards over blurred backgrounds).

**Color Palette:**
- Primary Violet: #8E44AD
- Secondary Pastel Purple: #C39BD3
- Accent Teal: #1ABC9C
- Background Light: #F6F4FB
- Background Dark: #1C1C1E
- Text Light: #2C2C2E
- Text Dark: #FFFFFF

**Typography:**
- Headlines: SF Rounded Pro (or system font with rounded design)
- Body Text: SF Pro Text
- Use Dynamic Type to respect user’s font-size settings.

**CSS/Styling in SwiftUI:**
- Custom view modifiers for consistent paddings, corner radius, shadows, and glass effect.
- Color and font styles defined in a central `Theme` struct for easy updates.

## Component Structure

**Folder Organization:**
- **Views/**: SwiftUI view files, one per screen or reusable component (e.g., `SoundRowView.swift`, `MixerView.swift`).
- **ViewModels/**: Classes conforming to `ObservableObject` for each screen (e.g., `SoundListViewModel.swift`).
- **Models/**: Plain Swift types for data (e.g., `Sound`, `Mix`).
- **Services/**: Singletons or protocol-based services (e.g., `AudioService`, `DownloadService`, `AuthService`).
- **Utilities/**: Helpers for common tasks (e.g., file cleanup, date formatters).
- **Assets/**: Color sets, image assets, audio placeholders.

**Reusability**
- Break complex screens into small SwiftUI components.
- Share common UI pieces (buttons, sliders) via custom SwiftUI views and modifiers.

## State Management

**SwiftUI State Properties**
- `@State` for simple, view-local state (e.g., slider positions).
- `@StateObject` for ViewModel instances to manage model data and business logic.
- `@ObservedObject` or `@EnvironmentObject` for sharing state between views.

**ViewModel Role**
- Expose `@Published` properties that drive UI updates.
- Handle network calls, file I/O, and audio engine commands using async/await.
- Centralize error handling and state transitions (loading, success, failure).

## Routing and Navigation

**NavigationStack & TabView**
- Use `NavigationStack` (iOS 16+) or `NavigationView` for push-based flows.
- Main tabs via `TabView`:
  1. Sounds List
  2. Mixer
  3. Saved Mixes
  4. Settings

**Flow Example**
- On startup, check authentication status:
  - If not logged in → present `LoginView`.
  - If logged in → show `MainTabView`.
- Within each tab, use navigation links to go deeper (e.g., sound detail).

## Performance Optimization

1. **Lazy Loading**
   - Use `LazyVStack`/`LazyHStack` for long lists of sounds.
2. **Code Splitting**
   - Keep services and view models separate for faster compile times.
3. **Audio Preloading**
   - Pre-buffer frequently used sounds for instant playback.
4. **Debouncing UI Events**
   - Throttle rapid slider changes before committing to the audio engine.
5. **Asset Optimization**
   - Compress audio files server-side; use appropriate bitrates.

These steps ensure low latency and smooth interactions.

## Testing and Quality Assurance

1. **Unit Tests**
   - XCTest for ViewModel logic, service methods, and utility functions.
2. **UI Tests**
   - XCUITest for critical flows: login, sound playback, mixer controls, timer.
3. **Snapshot Tests**
   - Capture SwiftUI views in light/dark mode to detect visual regressions.
4. **Static Analysis & Linting**
   - SwiftLint rules enforced in CI for code style consistency.
5. **Code Reviews**
   - Peer reviews on all pull requests to catch logic errors and maintain quality.

## Error Handling and Security

- **Error Handling**
  - Centralized error type for network, file I/O, and audio engine failures.
  - Show user-friendly alerts on failures (e.g., download errors, playback issues).
- **Security Measures**
  - Do not store sensitive info (like passwords) in clear text; use Keychain for tokens.
  - Validate all inputs and handle unexpected data gracefully.

## Analytics and Monitoring

- **GDPR-Compliant Analytics**
  - Track basic events: app launches, mix creations, downloads.
  - Anonymize user data and allow opt-out.
- **Crash Reporting**
  - Integrate a lightweight crash reporter to capture uncaught exceptions.

## Conclusion and Overall Frontend Summary

The Relaxing App’s frontend is built with SwiftUI and MVVM to ensure a clear separation of concerns and easy growth. Design focuses on usability, accessibility, and a dreamy aesthetic powered by pastel purples and glassmorphic touches. Component-based structure and Swift’s concurrency features guarantee smooth, low-latency audio mixing and offline functionality. Robust testing, error handling, and GDPR-compliant analytics round out a reliable, user-friendly experience. This setup aligns perfectly with our goals: a performant, maintainable, and delightful relaxation tool.