flowchart TD
  Start[Start App] --> Auth[Authentication]
  Auth --> AuthSuccess{Auth Success}
  AuthSuccess -->|Yes| Main[Main Screen]
  AuthSuccess -->|No| Login[Login Screen]
  Login --> Auth
  Main --> Catalog[Sound Catalog]
  Catalog --> Mixer[Sound Mixer]
  Mixer --> SaveMix{Save Mix}
  SaveMix -->|Yes| SaveName[Enter Mix Name]
  SaveName --> Saved[Mix Saved]
  SaveMix -->|No| Saved
  Saved --> Playback[Start Playback]
  Playback --> TimerSet{Set Timer}
  TimerSet -->|Yes| Timer[Configure Timer]
  TimerSet -->|No| PlaybackMode[Playback Mode]
  Timer --> PlaybackMode
  PlaybackMode --> Offline{Download for Offline}
  Offline -->|Yes| Download[Download Sounds]
  Offline -->|No| Continue[Continue Playback]
  Download --> Continue
  Continue --> Background[Background Playback]
  Background --> Settings[Settings]
  Settings --> Theme[Toggle LightDark Mode]
  Settings --> Preferences[Adjust Defaults]
  Settings --> Logout[Logout]
  Logout --> Auth