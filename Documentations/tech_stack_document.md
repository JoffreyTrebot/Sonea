# Tech Stack Document

This document explains the technology choices behind the Relaxing App in simple terms. You don’t need a technical background to understand why we picked each tool and how it helps build a reliable, smooth, and secure experience.

## 1. Frontend Technologies

These are the tools that run on your iPhone and shape what you see and interact with:

- **Swift & SwiftUI**
  - Swift is Apple’s modern programming language for iOS apps.
  - SwiftUI is a framework that makes it easy to build clean, responsive interfaces with live previews as we code.
  - Together, they let us create smooth animations, dark/light themes, and a layout that automatically adapts to different iPhone sizes.

- **MVVM (Model-View-ViewModel) Pattern**
  - Separates what you see (View) from the data (Model) and the logic that connects them (ViewModel).
  - Helps keep the code organized, makes it easier to add new features, and reduces bugs.

- **Swift Concurrency (async/await)**
  - Handles tasks like downloading sounds or saving mixes without freezing the interface.
  - Ensures that long tasks run in the background while the app remains responsive.

- **AudioEngine (AVAudioEngine)**
  - Apple’s built-in audio framework for playing and mixing sounds.
  - Provides smooth fade-in/fade-out transitions and low-latency playback for a high-quality listening experience.

- **FileManager (iOS)**
  - Manages saving and reading downloaded audio files on the device.
  - Lets users play their favorite mixes offline.

- **Xcode**
  - Apple’s official app development environment.
  - Offers code suggestions, a built-in simulator, and direct connection to real devices for testing.

## 2. Backend Technologies

These components live “in the cloud” and support core features like user login, data storage, and file hosting:

- **Supabase**
  - An all-in-one backend service built on a PostgreSQL database.
  - **Authentication**: Handles email/password and Apple Sign-In securely.
  - **Database (PostgreSQL)**: Stores user profiles, available sounds, and custom mixes.
  - **Storage**: Hosts original audio files so you can stream or download them.

- **REST APIs**
  - Swift code calls Supabase’s APIs to fetch sound lists, save mixes, and manage downloads.
  - Ensures data stays in sync between your device and the cloud.

- **DownloadManager & AudioManager Services**
  - Custom Swift classes that wrap file downloads, caching rules, and multi-track playback.
  - Automatically remove old downloads when you’re low on space.

## 3. Infrastructure and Deployment

Here’s how we build, test, and deliver new versions of the app reliably:

- **Version Control (Git & GitHub)**
  - Every change to the code is tracked, reviewed, and stored safely in a shared repository.
  - Allows multiple developers to collaborate without overwrite conflicts.

- **CI/CD (Continuous Integration / Continuous Deployment)**
  - We use automated workflows (for example, GitHub Actions or Xcode Cloud) to build the app on each code update.
  - Runs basic tests, code formatting checks, and delivers a new beta to TestFlight for easy testing.

- **App Store Connect & TestFlight**
  - Apple’s platform for distributing beta builds and submitting the final app to the App Store.
  - Ensures a smooth review and release process.

- **Supabase Hosting**
  - Supabase manages the backend servers, database, and file storage so we don’t have to maintain our own infrastructure.
  - Scales automatically as more users join.

## 4. Third-Party Integrations

These extra services add important features without reinventing the wheel:

- **LaSonotheque.org**
  - Source of high-quality, public-domain nature sound files (forest, rain, ocean, birds, etc.).
  - No licensing fees required.

- **GDPR-Compliant Analytics**
  - Tracks basic usage patterns (e.g., which sections people use most) without collecting personal data.
  - Lets users opt in or out in settings.

- **Crash Reporting**
  - Automatically logs unexpected errors so we can fix issues quickly.
  - No sensitive user data is stored in crash reports.

## 5. Security and Performance Considerations

We designed the app to be safe and smooth for everyone:

- **Secure Authentication & Data Transfer**
  - All logins and data requests go over HTTPS.
  - User passwords are never stored on the device in plain text.

- **Offline-First & Cache Management**
  - Downloads are stored securely in the app’s private folder.
  - Automatic cleanup of the oldest files when storage is low keeps offline usage trouble-free.

- **Non-Blocking UI**
  - Network calls and audio decoding run in the background with async/await.
  - No freezing screens or dropped frames, even with multiple sounds playing.

- **Smooth Audio Experience**
  - Fade-in, fade-out, and crossfades reduce abrupt starts or stops.
  - Sleep timer gracefully fades out tracks and sends a local notification when time’s up.

## 6. Conclusion and Overall Tech Stack Summary

Relaxing App’s technology choices focus on reliability, performance, and ease of maintenance, while delivering a delightful user experience:

- We build the interface natively with **SwiftUI**, following the **MVVM** pattern for clean code.
- **Supabase** powers secure user management, a robust database, and audio file hosting.
- Background tasks use **Swift Concurrency** to keep the app snappy.
- **AVAudioEngine** and **FileManager** handle high-quality playback and offline storage.
- Automated workflows and **GitHub/Git** ensure every release is tested and deployed smoothly.
- GDPR-friendly analytics and crash reporting help us improve the app without compromising your privacy.

Together, these tools and services create a scalable, maintainable, and user-friendly app that helps you relax, sleep better, and enjoy custom soundscapes—anytime, anywhere.