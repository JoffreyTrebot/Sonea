# Project Requirements Document (PRD)

## 1. Project Overview

Relaxing App is a native iOS application built in Swift using SwiftUI and the MVVM (Model-View-ViewModel) architecture. It helps users reduce stress and improve sleep by letting them mix, save, and play custom ambient soundscapes. Users choose from nature sounds (rain, forest, ocean, birds, etc.), adjust each track’s volume, and save their favorite combinations for later.

This app connects to Supabase for user authentication (email/password + Apple Sign-In), cloud storage of audio files, and a PostgreSQL database of sounds and mixes. Key objectives for version 1 are:

*   Let users stream or download sounds for offline use
*   Offer a simple but powerful mixer with independent volume controls
*   Ensure reliable background playback with a sleep timer
*   Support French and English languages
*   Collect only basic, GDPR-compliant analytics and crash reports

Success means the app runs smoothly on recent iPhones, users can easily create and revisit mixes, and there are zero critical crashes or data leaks.

## 2. In-Scope vs. Out-of-Scope

### In-Scope (Version 1)

*   User onboarding and authentication via Supabase (email/password + Apple Sign-In)
*   Browsing a curated library of natural sounds (stream or download)
*   Visual indicators for offline-available sounds
*   Mixer interface to play multiple sounds with independent volume sliders
*   Save and list custom mixes in **My Mixes**
*   Offline download management with automatic purge when storage limit is reached
*   Background audio playback when the app is locked or in the background
*   Sleep timer to fade out and stop audio, even if the device is locked
*   Settings: light/dark mode, default timer duration, auto-delete toggle, analytics opt-in/out
*   Bilingual support: French and English
*   Basic, GDPR-compliant analytics and crash reporting

### Out-of-Scope (Planned for Later Phases)

*   Social sharing or export of mixes
*   In-app purchases, ads, or subscription tiers
*   Brand-new sound packs or premium content
*   Advanced audio effects (beyond fade-ins/fade-outs and crossfades)
*   Full-scale user behavior analytics dashboards
*   Multi-platform support (Android, web)

## 3. User Flow

When a new user opens the app, they see a branded splash screen that transitions into a login/register screen. They can sign up with an email and password or use Apple Sign-In. Once authenticated, the user lands on the **MainTabView**, which features four tabs at the bottom: **Mixer**, **My Mixes**, **Downloads**, and **Settings**. The active tab is highlighted in pastel purple, and the background color shifts subtly to keep the relaxing atmosphere.

In the **Mixer** tab, users see a palette of available sounds, each showing a waveform icon and a download/play toggle. Tapping play streams the sound (with a fade-in), and a slider appears to control its volume. Users combine several tracks, adjust levels in real time, then tap “Save” to name and store the mix. In **My Mixes**, saved mixes appear as rounded cards; tapping one instantly loads it into the mixer with smooth crossfades. In **Downloads**, users manage offline tracks; old files are auto-deleted when storage is low, or they can delete manually via long-press. The **Settings** tab lets users switch appearance modes, set default timer lengths, toggle auto-cleanup, and opt in/out of GDPR-compliant analytics and crash reporting.

## 4. Core Features

*   **Authentication & User Management**\
    • Email/password sign-up and login via Supabase\
    • Apple Sign-In integration\
    • Error handling and inline feedback\
    • Logout functionality
*   **Sound Catalog & Streaming**\
    • Fetch sound list from Supabase Storage\
    • Stream audio with AVAudioEngine and async/await\
    • Download audio files locally\
    • Visual indicators for offline availability\
    • Manual deletion of downloaded tracks
*   **Mixer & Custom Mixes**\
    • Layer multiple nature sounds simultaneously\
    • Independent volume sliders per track\
    • Fade-in/fade-out and crossfade between loops\
    • Save new mixes with custom names\
    • Load and play saved mixes in one tap\
    • Swipe-to-delete mixes (with confirmation)
*   **Offline Download & Storage Management**\
    • Store downloads in Application Support directory using FileManager\
    • Detect existing downloads and prevent duplicates\
    • Auto-purge least recently used files when cache limit is reached
*   **Playback Control & Timer**\
    • Background audio continues when the device is locked or app is in the background\
    • User-set sleep timer that fades out audio and stops playback\
    • Local notification on timer completion
*   **User Settings & Personalization**\
    • Light/dark mode (system-driven or manual)\
    • Default timer duration adjustment\
    • Toggle automatic deletion of old downloads\
    • Analytics opt-in/out (GDPR-compliant)\
    • French and English localization
*   **Analytics, Security & Performance**\
    • Basic event tracking and crash reporting\
    • No sensitive data stored in plaintext\
    • Swift Concurrency for all network and I/O tasks\
    • High-performance AVAudioEngine setup

## 5. Tech Stack & Tools

*   **Frontend & Language**:\
    • Swift, SwiftUI (100% UI)\
    • MVVM architecture
*   **Backend & Services**:\
    • Supabase Auth (email/password + Apple)\
    • Supabase Storage (audio files)\
    • Supabase PostgreSQL database (sound/meta, user mixes)
*   **Audio & File Management**:\
    • AVAudioEngine for multi-track playback and audio transitions\
    • FileManager for local file storage in Application Support
*   **Concurrency & Networking**:\
    • Swift Concurrency (async/await) for all REST calls and I/O
*   **Development & AI-Assisted Coding**:\
    • Xcode\
    • Cursor (AI-powered IDE plugin)\
    • Claude 3.7 Sonnet, Claude 3.5 Sonnet, Gemini 2.5 Pro for code suggestions
*   **Analytics & Crash Reporting**:\
    • GDPR-compliant basic analytics library\
    • Crash reporting SDK (e.g., Sentry or Firebase Crashlytics)

## 6. Non-Functional Requirements

*   **Performance**\
    • App launch < 2 seconds on recent devices\
    • Audio buffer latency < 100 ms for mixer interactions\
    • Smooth UI animations at 60 fps
*   **Security & Privacy**\
    • All network traffic over HTTPS\
    • No sensitive data stored in cleartext\
    • GDPR compliance for analytics (opt-in/out, data retention)
*   **Usability & Accessibility**\
    • Support Dynamic Type (font sizes adjustable)\
    • VoiceOver labels on all interactive elements\
    • High-contrast color palette in both light/dark modes
*   **Reliability**\
    • 99.9% uptime for Supabase services\
    • Robust error handling for network and audio failures

## 7. Constraints & Assumptions

*   iOS 15+ target deployment
*   Supabase services (Auth, Storage, Database) fully operational
*   Audio files licensed with no restrictions (public domain source: LaSonotheque.org)
*   Device will grant background audio permissions
*   Maximum cache size threshold defined by product team (e.g., 500 MB)
*   Users will grant analytics opt-in if they wish to help improve the app

## 8. Known Issues & Potential Pitfalls

*   **API Rate Limits**: Supabase may throttle REST requests if too frequent.\
    • Mitigation: Batch requests where possible; use client-side caching.
*   **Background Audio Restrictions**: iOS may suspend tasks in the background.\
    • Mitigation: Configure `AVAudioSession` category to `.playback`; enable Background Modes in entitlements.
*   **Storage Management Edge Cases**: Purging files while they’re playing or in use could cause errors.\
    • Mitigation: Lock files in use; only delete when playback stops.
*   **Crossfade Glitches**: Overlapping fades could introduce pops or silence gaps.\
    • Mitigation: Tune fade durations; test extensively on different devices.
*   **Localization Gaps**: Missing translations or plural rules can break UI.\
    • Mitigation: Use localized string tables; include pseudolocalization in QA.

This document contains all the information an AI model needs to generate downstream technical specifications—Tech Stack docs, Frontend Guidelines, Backend Structure, App Flowcharts, and beyond—without guessing or ambiguity.
