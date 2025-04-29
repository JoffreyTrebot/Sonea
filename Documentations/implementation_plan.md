# Implementation plan

## Phase 1: Environment Setup

1. **Prevalidation**: Check if the project root contains an Xcode project (`*.xcodeproj`) or workspace (`*.xcworkspace`). If not, proceed to Step 2. (Project Summary)
2. **Initialize Xcode Project**: Open Xcode 15.x and create a new iOS App named `RelaxingApp` using SwiftUI and MVVM architecture. Save in project root. (Tech Stack: Frontend)
3. **Install Homebrew & Git**: If missing, run `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` and `brew install git`. (Tech Stack: Core Tools)
4. **Validation**: Run `brew --version` and `git --version` to confirm correct installations. (Tech Stack: Core Tools)
5. **Install Xcode CLI Tools**: Run `xcode-select --install` if needed. (Tech Stack: Core Tools)
6. **Validation**: Run `xcode-select -p` and ensure it returns a valid path. (Tech Stack: Core Tools)
7. **Create `.cursor` Directory**: In project root, run `mkdir -p .cursor`. (Tech Stack: AI Coding Assistants)
8. **Create `cursor_metrics.md`**: Inside `.cursor`, create `cursor_metrics.md` (empty) to enable Cursor metrics. (Tech Stack: AI Coding Assistants)
9. **Create & Ignore `mcp.json`**: In `.cursor`, create `mcp.json` and add `.cursor/mcp.json` to `.gitignore`. (Tech Stack: AI Coding Assistants)
10. **Configure MCP Servers**: Add the following JSON to `.cursor/mcp.json`, replacing `<connection-string>` later:
    ```json
    {
      "mcpServers": {
        "supabase": {
          "command": "npx",
          "args": ["-y", "@modelcontextprotocol/server-postgres", "<connection-string>"]
        }
      }
    }
    ```
    (Tech Stack: AI Coding Assistants)
11. **Get Supabase Connection String**: Display this link for obtaining it: https://supabase.com/docs/guides/getting-started/mcp#connect-to-supabase-using-mcp (Tech Stack: AI Coding Assistants)
12. **Add Supabase Swift SDK**: In Xcode, go to File → Swift Packages → Add Package Dependency `https://github.com/supabase-community/supabase-swift.git` @ `1.5.0`. (Tech Stack: Backend)
13. **Validation**: In Xcode’s Swift Package manager, confirm `supabase-swift 1.5.0` is installed. (Tech Stack: Backend)

## Phase 2: Frontend Development (SwiftUI)

14. **Create Authentication View**: Create `/RelaxingApp/Views/AuthView.swift`. (Project Summary)
15. **Add Input Fields**: In `AuthView.swift`, add `TextField` for email and `SecureField` for password with client-side regex validation. (Project Summary)
16. **Add Apple Sign-In Button**: In `AuthView.swift`, import `AuthenticationServices` and add `ASAuthorizationAppleIDButton`. (Project Summary)
17. **Create AuthViewModel**: Create `/RelaxingApp/ViewModels/AuthViewModel.swift` implementing `ObservableObject`. (Project Summary)
18. **Implement Auth Logic**: In `AuthViewModel`, add `func signUp()` and `func signIn()` using `SupabaseClient.auth.signUp` and `.signIn`. (Tech Stack: Backend)
19. **Validation**: Create `/RelaxingApp/Tests/AuthViewModelTests.swift` and run `xcodebuild test -scheme RelaxingApp -only-testing RelaxingAppTests/AuthViewModelTests`. (Tech Stack: Testing)
20. **Create Sound List View**: Create `/RelaxingApp/Views/SoundListView.swift`. (Project Summary)
21. **Fetch Sound List**: In `SoundListView`, call `SupabaseClient.storage.from("sounds").list()` to retrieve available sounds. (Project Summary)
22. **Display Items**: Render each sound as a row with name, play/download icons, and offline-indicator badge. (UI/UX Design)
23. **Create SoundListViewModel**: Create `/RelaxingApp/ViewModels/SoundListViewModel.swift` implementing fetch and state logic. (Project Summary)
24. **Validation**: Add `/RelaxingApp/Tests/SoundListViewModelTests.swift` and run tests via `xcodebuild`. (Tech Stack: Testing)
25. **Create Mixer View**: Create `/RelaxingApp/Views/MixerView.swift`. (Project Summary)
26. **Create MixerViewModel**: Create `/RelaxingApp/ViewModels/MixerViewModel.swift`. (Project Summary)
27. **Implement Audio Engine**: In `/RelaxingApp/Services/AudioEngineManager.swift`, use `AVAudioEngine` to load multiple `AVAudioPlayerNode` instances, implementing fade-in/fade-out crossfades. (Project Summary)
28. **Create Download Manager**: Create `/RelaxingApp/Services/DownloadManager.swift` to download/delete sounds via `FileManager`. (Project Summary)
29. **Add Download/Delete UI**: In `SoundListView.swift`, wire download and delete buttons to `DownloadManager`. (Project Summary)
30. **Validation**: Download a sound in Simulator and verify file existence in sandbox via Xcode’s Devices & Simulators. (Non-Functional Requirements)
31. **Create Timer View**: Create `/RelaxingApp/Views/TimerView.swift`. (Project Summary)
32. **Create TimerViewModel**: Create `/RelaxingApp/ViewModels/TimerViewModel.swift`. (Project Summary)
33. **Implement Timer Logic**: In `TimerViewModel`, use Swift Concurrency `Task.sleep` for shutoff after the selected duration. (Project Summary)
34. **Validation**: Start timer and confirm audio stops after the set duration. (Non-Functional Requirements)
35. **Create Settings View**: Create `/RelaxingApp/Views/SettingsView.swift`. (Project Summary)
36. **Create SettingsViewModel**: Create `/RelaxingApp/ViewModels/SettingsViewModel.swift`. (Project Summary)
37. **Persist Settings**: In `SettingsViewModel`, upsert into Supabase table `user_settings` (`user_id`, `default_timer_duration`, `auto_delete_old_downloads`, `analytics_opt_in`, `language`). (Project Summary)
38. **Implement Dark/Light Mode**: In `SettingsView.swift`, add toggle bound to `@AppStorage("isDarkMode")` and apply `ColorScheme`. (UI/UX Design)
39. **Implement Language Selection**: Add picker for English/French storing choice in `SettingsViewModel`. (Project Summary)
40. **Validation**: Switch language in Settings and verify UI text updates accordingly. (Non-Functional Requirements)
41. **Add Analytics Service**: Create `/RelaxingApp/Services/AnalyticsService.swift` to handle GDPR-compliant opt-in/out and integrate crash reporting. (Non-Functional Requirements)

## Phase 3: Backend Development (Supabase)

42. **Define Database Schema**: Draft SQL for tables `mixes`, `user_settings`, and `downloads`, with appropriate types and foreign keys. (To-Do List)
43. **Document Migrations**: Save SQL in `/supabase/migrations/2024-06-01_init.sql`. (To-Do List)
44. **Apply Migrations**: Run `npx -y @modelcontextprotocol/server-postgres <connection-string>` via the Cursor MCP server to create tables. (Tech Stack: Backend)
45. **Validation**: In Supabase Dashboard SQL editor, run `SELECT * FROM mixes;` to confirm table creation. (Tech Stack: Backend)

## Phase 4: Integration

46. **Set Root View Based on Auth**: In `/RelaxingApp/RelaxingAppApp.swift`, observe `AuthViewModel.$session` to switch between `AuthView` and `SoundListView`. (App Flow)
47. **Enable Background Audio**: Open `Info.plist` and add `UIBackgroundModes` → `audio`. (Project Summary)
48. **Validation**: Build and run on device, lock screen, and confirm audio continues playing. (Project Summary)

## Phase 5: Deployment

49. **Configure App Store Connect**: In your Apple Developer account, create new app record for `RelaxingApp`, set bundle ID, enable Sign In with Apple. (To-Do List)
50. **Archive & Distribute**: In Xcode, select Generic iOS Device → Product → Archive → Distribute to TestFlight; confirm build availability in App Store Connect. (To-Do List)