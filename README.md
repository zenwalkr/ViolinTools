# Violin Ear Training

Violin Ear Training is a browser-based pitch-tracking and intonation practice tool for violinists. It listens to a microphone in real time or analyzes an uploaded audio/video recording, turns detected pitches into a note sequence, and shows how accurately each note was played.

The current UI identifies itself as version **2.7.2 (MIDI Import)**.

## What it does

- Detects pitch from a live microphone input.
- Analyzes uploaded audio and video files.
- Imports Standard MIDI files (`.mid` and `.midi`) for playback and editing.
- Displays the current note, frequency, and cents deviation on a tuning gauge.
- Scores intonation using a 15-cent tolerance and assigns a letter grade.
- Shows detected notes with timing, duration, volume, pitch, and violin fingering information.
- Renders a violin tablature view with playback, copying, transposition, and manual phrase breaks.
- Shows the likely top three keys for the detected material.
- Provides an interactive G-D-A-E fingerboard; tap a position to hear its pitch.
- Displays a moving-average intonation graph.
- Replays a detected or imported phrase with a selectable General MIDI instrument.
- Saves practice sessions locally and lets you restore, rename, or delete them.
- Exports and imports settings, history, and workspace data as `.vpack` backup files.
- Includes a focused Ear Training (Zen) mode for practice without the full workspace.

## Quick start

This is a static web app. No build step or server-side code is required.

1. Download or clone the repository.
2. Open `index.html` in a modern browser, or serve the directory from a local web server.
3. Click **Start** and grant microphone permission when prompted.
4. Play one note at a time with a clear attack. Detected notes appear in the log and tablature.
5. Use **Replay** to hear the captured phrase, **Save** to keep the session, or **MIDI** to export it.

For the most reliable microphone behavior, use a local web server instead of opening the file directly:

```bash
python3 -m http.server 8000
```

Then visit <http://localhost:8000>.

## Browser requirements

Use a current desktop or mobile browser with support for:

- Web Audio API and `AudioContext`.
- `navigator.mediaDevices.getUserMedia()` for microphone input.
- `localStorage` for settings and practice history.
- File uploads and Blob downloads.

Microphone access normally requires a secure context (`https://`) or `localhost`. The app includes an iOS-specific video pipeline for more stable uploaded-video decoding.

## Main workflow

### Live microphone practice

1. Select a detection engine from the header or Settings.
2. Click **Start** and allow microphone access.
3. Play sustained, isolated notes. The gauge shows the detected pitch and cents offset.
4. Watch the detected-note log and intonation graph as you play.
5. Click **Save** to archive the current practice session.

The app applies microphone gain, noise-floor handling, clarity filtering, note-duration filtering, damping, and silence timeout settings to stabilize detection.

### Audio and video analysis

1. Click **Upload**.
2. Choose an audio or video file.
3. Start the video/audio analysis when the playback overlay appears.
4. Review the resulting note sequence, score, key estimate, tablature, and graph.

The upload control accepts common browser-supported audio/video types as well as MIDI files.

### MIDI import

Choose a `.mid` or `.midi` file with **Upload**. Before processing, the MIDI dialog lets you configure:

- Transposition from -24 to +24 semitones.
- Monophonic handling for overlapping notes.
- Minimum note duration filtering.
- How MIDI velocity maps to playback volume.

Click **Process** to place the imported material in the workspace. Imported material can be edited, transposed, replayed, saved, exported, or copied as tablature.

## Understanding the interface

- **Start / Stop** — toggles live microphone listening.
- **Upload** — imports audio, video, or MIDI.
- **Replay** — plays the current sequence using the selected instrument.
- **MIDI** — downloads the current sequence as a MIDI file.
- **Save** — stores the current session in local practice history.
- **Reset** — clears the workspace and auto-saves the prior state.
- **Settings** — opens engine, playback, display, backup, and practice options.
- **Detected Notes** — shows individual notes; click a note to edit it.
- **Tablature** — shows violin-string/finger positions and phrase segments.
- **Detected Key** — estimates major/minor key candidates from recent notes.
- **Finger Grid** — summarizes notes by G, D, A, and E strings across first through third position.
- **Intonation Trend** — plots cents deviation over time or by moving-average window.
- **Practice History** — restores or manages saved sessions.

Most sections can be collapsed from their header. Display toggles in Settings control which sections and note details are visible.

## Detection engines

The app provides three client-side pitch detectors:

- **Precision (YIN)** — the high-accuracy option for careful intonation work.
- **Basic (Auto-Corr)** — lower latency and lighter processing.
- **Harmonic (HPS)** — designed to cope better with the rich harmonic content of violin tones.

The Settings panel also provides detection profiles and controls for damping, minimum note duration, silence timeout, A4 reference pitch, clarity threshold, and microphone gain. Use **Reset Engine to Defaults** if experimentation produces unstable results.

## Scoring and grades

Intonation scoring uses the absolute cents deviation from the target pitch:

- `0¢` = 100%.
- `±15¢` = the zero-percent boundary.
- Deviations beyond `±15¢` can produce negative scores.

The displayed grade scale is:

| Grade | Score |
| --- | ---: |
| A+ | 95–100% |
| A | 80–94% |
| A- | 70–79% |
| B+ | 60–69% |
| B | 50–59% |
| B- | 40–49% |
| C+ | 30–39% |
| C | 20–29% |
| C- | 10–19% |
| D | 0–9% |
| F | below 0% |

By default, open strings can be excluded from the overall score, grade, and key calculation. This can be changed with **Exclude Open Strings from Scoring**.

## Editing notes and tablature

Click any detected note in the log or tablature to open **Note Details**. You can:

- Change its pitch using the violin-position-sorted note selector.
- Adjust duration and volume.
- Insert a note before or after it.
- Delete it.
- Add or remove a manual phrase break before or after it.

Changing a note duration shifts the following notes so the sequence remains continuous. In the tablature toolbar you can also:

- Play the whole tablature.
- Join all manual breaks.
- Copy a text tablature representation.
- Transpose every note by semitone steps or reset the transpose offset.

## Playback and instruments

Playback uses Web Audio. The app first attempts to use `soundfont-player` with a General MIDI instrument and falls back to a basic oscillator if the SoundFont cannot be loaded. The instrument list includes piano, strings, guitar, brass, woodwind, synth, percussion, and sound-effect programs.

The page loads `soundfont-player` v0.12.0 from jsDelivr. An internet connection may therefore be needed for the full SoundFont playback experience; pitch detection and basic oscillator fallback remain browser-side features.

## Settings

Settings include:

- Detection engine and profile.
- Damping, minimum note duration, silence timeout, A4 reference, clarity threshold, and mic gain.
- MIDI instrument and playback volume.
- Moving-average or time-window graph display.
- Ear Training (Zen) mode.
- Visibility of key, tablature, finger positions, finger grid, durations, timestamps, and dynamic background color.
- BPM calculation.
- Open-string scoring and highlighting.
- Recent-note limiting.
- Playback gap filling.
- Debug diagnostics.

Use **Reset All Toggles to Defaults** to restore display and practice preferences.

## Backups and local data

Settings, manual phrase breaks, and practice history are stored in the browser's `localStorage` under the app's own keys. They are not uploaded by the app.

Use **Settings → Backup & Restore → Export .vpack** to download a portable backup containing the workspace, settings, and history. Use **Import .vpack** to restore that data on another browser or device. Treat backup files as private because they may contain your practice history and configuration.

Clearing browser site data can remove unsaved local sessions. Export a `.vpack` before clearing storage or changing browsers.

## Keyboard shortcuts

- **Space** — start or stop listening.
- **R** — replay the current sequence.
- **S** — save the current session.

## Troubleshooting

### The microphone does not start

- Serve the app from `localhost` or HTTPS.
- Allow microphone permission for the site.
- Check that another application has not exclusively claimed the microphone.
- Try a current Chromium, Firefox, or Safari release.
- Reload after changing the permission decision.

### Notes are inaccurate or jumpy

- Play one sustained note at a time and reduce room noise.
- Move closer to the microphone without clipping the input.
- Raise the clarity threshold for cleaner detections, or lower it for quieter playing.
- Try Precision or Harmonic mode.
- Adjust mic gain and damping gradually.
- Use the A4 reference setting that matches the instrument or recording.

### Uploaded video will not play

- Use a browser-supported video format.
- Tap the playback overlay to satisfy the browser's user-gesture requirement.
- On iOS, wait for the video pipeline to initialize and start it from the overlay.

### Replay has no SoundFont instrument

- Confirm the browser can reach jsDelivr.
- Click or tap the page first so the browser can resume the audio context.
- Check the diagnostic panel for SoundFont status.
- The app will fall back to an oscillator when SoundFont loading fails.

### MIDI sounds too busy

- Enable monophonic mode in the MIDI import dialog.
- Increase the minimum note duration.
- Select a different velocity-to-volume mode.
- Use the note editor to delete or join unwanted material.

## Project structure

```text
.
├── index.html                 # Complete application UI, styles, and client logic
├── soundfont-player.min.js    # Bundled SoundFont Player asset retained with the project
└── README.md                  # This documentation
```

The main application is intentionally packaged as one static HTML file so it can be hosted from a simple static server. There is no build system, package manager, backend, or database. The current HTML imports SoundFont Player from jsDelivr; the local copy is included as a project asset but is not the active script import.

## Development and deployment

For local development, edit `index.html` and refresh the browser. A static host such as GitHub Pages, Netlify, or any ordinary web server can serve the project. Configure the deployed site for HTTPS if microphone access is required.

Before publishing changes, manually verify:

1. The page loads without console errors.
2. Microphone permission and Start/Stop work.
3. Audio/video upload and MIDI import work.
4. Replay and MIDI export work.
5. Save, restore, export, and import preserve data.
6. The layout remains usable on a narrow mobile viewport.

## Privacy

Pitch detection and file analysis are performed in the browser. The application uses microphone input only after you start listening and uses local browser storage for settings and history. Uploaded media is processed by the page for analysis and is not sent to an application server by this project. The external SoundFont CDN request is separate from analysis and is used for instrument playback.

## License

No license file is currently included. Add a license before redistributing the project if you want to grant explicit reuse permissions.
