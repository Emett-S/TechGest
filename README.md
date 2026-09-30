# TechGest

TechGest is a Windows desktop application for controlling a computer with hand gestures detected by a webcam. It uses OpenCV for video capture, MediaPipe for hand landmarks, and PyQt5 for the user interface. Gestures can move the cursor, click the mouse, press keyboard keys, control volume and brightness, scroll, or trigger system shortcuts.

The application starts in a small 3D sphere mode. Clicking the sphere opens the main window and starts the camera. The project includes `TechGest.lnk` for the Windows desktop and `TechGest.ico`, used by the shortcut, application window, taskbar, and minimize button.

## Features

- Real-time tracking of one or two hands.
- Short gesture history to prevent one noisy frame from triggering an action.
- Cursor control using the index finger.
- Left, right, and middle mouse clicks.
- Keyboard keys, modifier keys, arrows, and `F1`–`F12`.
- Volume, brightness, zoom, scrolling, and directional key actions.
- Windows shortcuts such as copy, paste, task switching, lock, and screenshot.
- Optional animated nanotech face mask.
- 3D sphere mode with automatic idle dimming.
- Rule editor with JSON import and export.
- English, Polish, Spanish, Chinese, Hindi, and Arabic interface languages.
- Custom hand and mask colors and multiple animation styles.
- Camera retry logic and safe camera shutdown.
- A button for restoring default settings.

## Supported gestures

The following gestures are available in the rule editor:

| Gesture | Typical use |
| --- | --- |
| Pinch | Brightness, volume, zoom, or another slider action |
| OK Gesture | A configurable command |
| Pistol | A configurable command |
| Open Hand | A configurable command |
| Fist | A configurable command |
| Pointing Finger | Cursor control or a configurable command |
| Peace / Victory | A configurable command |
| Four Fingers | A configurable command |
| Spiderman Gesture | A configurable command |
| Thumbs Up | A configurable command |
| Thumbs Down | A configurable command |

Special two-hand gestures:

| Gesture | Action |
| --- | --- |
| Finger Frame | Opens the Windows snipping tool (`Win` + `Shift` + `S`) |
| Pyramid | Toggles the nanotech face mask |
| ET Touch | Detects two index fingertips touching or very close together |

MediaPipe determines the left and right hand automatically. A short history of detected frames reduces flicker and prevents an action from being triggered by a single incorrect frame.

## Action categories

### Mouse

Mouse rules can control the cursor with the pointing finger and perform left, right, or middle clicks. Cursor movement is smoothed to make the pointer easier to control.

### Keyboard

Keyboard rules can send normal letters and numbers, special keys, arrow keys, modifier keys, and function keys from `F1` to `F12`. This allows gestures to control applications without touching the physical keyboard.

### Sliders and movement

Available actions include brightness, system volume, zoom, mouse wheel scrolling, horizontal and vertical movement, and directional key actions. Hardware brightness support depends on the computer and monitor.

### System actions

System rules include copy, paste, select all, undo, task switching, lock, screenshot, and other Windows shortcuts. Power-related actions should be assigned carefully because they can close applications or shut down the computer.

## Creating and editing a rule

1. Open the main window by clicking the 3D sphere.
2. Select a hand option, gesture, action category, and action.
3. Click **Add rule**.
4. Enable gesture detection with the **Detection** control.
5. Select a rule and use the delete button when it is no longer needed.

Rules are saved to `gestures_config.json`. The file can be exported as a backup and imported on another installation. Internal action names are kept in their canonical form so existing configurations remain compatible when the interface language changes.

## 3D sphere mode

TechGest starts in a compact 3D sphere mode. Clicking the sphere opens the main window and starts the camera. Hold the `3` key while dragging the sphere to move it around the desktop. When the sphere has not been used for a few seconds, it dims to 40% opacity. Moving the pointer over it restores 100% opacity. Switching from the main window to sphere mode keeps it fully visible briefly so it is easy to find.

Right-clicking the sphere opens its context menu, where detection can be controlled or the application can be closed. The same `TechGest.ico` file is used for the sphere/minimize control, the main window, the taskbar icon, and the desktop shortcut.

## Settings

The settings window contains:

- Interface language: English, Polish, Spanish, Chinese, Hindi, or Arabic.
- Nanotech face mask enable/disable option.
- Mask color and hand color.
- Animation style: Default, Iron Man, Wave, Lightning, Slow, Neon, Crystallization, Pixelated, Spiral, or Holographic.
- A **Restore defaults** button that returns all settings and rules to their original values.

The selected language changes all user-facing labels, buttons, menus, messages, and settings text. Restart the application if Windows or a third-party component still displays an old label.

## JSON import and export

Configuration files contain `rules` and `settings`. A minimal example is:

```json
{
  "rules": [
    {"hand": "Dowolna", "gesture": "Palec Wskazujący", "cat": "Myszka", "act": "Kursor"}
  ],
  "settings": {
    "language": "en",
    "mask_color": [10, 10, 10],
    "animation_style": "Domyślna",
    "hand_color": [112, 112, 112]
  }
}
```

Canonical rule names remain unchanged for compatibility with older project files. Use the import/export buttons instead of manually editing a file while the application is running.

## Installation on Windows

### Requirements

- Windows 10 or Windows 11.
- Python 3.11 or newer.
- A working webcam and permission for desktop applications to use it.
- A monitor or laptop that supports software brightness control if brightness gestures are required.

### Install from the repository

```powershell
git clone https://github.com/YOUR_USERNAME/TechGest.git
cd TechGest
py -3.11 -m venv env
.\env\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If PowerShell blocks activation, allow scripts for the current user and then activate the environment again:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
.\env\Scripts\Activate.ps1
```

The required Python packages are listed in `requirements.txt`:

- `pynput` for mouse and keyboard input.
- `mediapipe` for hand landmark detection.
- `opencv-contrib-python` for camera capture and image processing.
- `PyQt5` for the desktop interface and sphere window.
- `screen-brightness-control` for supported brightness devices.

### Start the application

```powershell
python main.py
```

To open the full interface directly:

```powershell
python main.py --full
```

The included `TechGest.lnk` shortcut can be copied to the desktop. It starts the program with the project environment and uses `TechGest.ico` as its icon.

## Camera troubleshooting

If the preview is black, frozen, or shows corrupted RGB-like pixels after restarting:

1. Open **Windows Settings > Privacy & security > Camera** and allow camera access for desktop applications.
2. Close Teams, Zoom, Discord, browsers, or other software that may already own the camera.
3. Disconnect and reconnect an external webcam, then restart TechGest.
4. TechGest first tries the DirectShow backend and then the default OpenCV backend. It also retries failed reads and safely copies frames before processing them.
5. Check `techgest.log` in the project folder for camera index, backend, and frame-read diagnostics.
6. If more than one camera is connected, temporarily disconnect the unused camera and try again.

The application should not terminate when a camera frame or gesture cannot be recognized. Such events are logged and the main loop continues. If a camera is unavailable, the interface remains open so the device can be reconnected.

## Project structure

```text
TechGest/
├── main.py                 # Main application, UI, camera loop, and gesture dispatch
├── requirements.txt        # Python dependencies
├── gestures_config.json    # User gesture rules and settings
├── data/config.json        # Additional application configuration
├── TechGest.ico            # Shared application and sphere icon
├── techgest.log            # Runtime and camera diagnostics
├── models/                 # Optional local model assets
├── gestures/static/        # Gesture and mask resources
├── actions/                # Input and system action helpers
├── core/                   # Shared application and configuration helpers
└── env/                    # Local virtual environment; do not publish it
```

The exact contents of `models`, `gestures`, `actions`, and `core` can vary between releases. Do not commit a local virtual environment, cache directories, private logs, or personal configuration backups.

## Publishing on GitHub

Before the first commit, create a `.gitignore` file with at least:

```gitignore
env/
__pycache__/
*.pyc
.idea/
.vscode/
techgest.log
```

Then publish the project:

```powershell
git init
git add main.py requirements.txt gestures_config.json data gestures actions core models TechGest.ico README.md .gitignore
git commit -m "Initial TechGest release"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/TechGest.git
git push -u origin main
```

Replace `YOUR_USERNAME` with the GitHub account or organization that owns the repository. Do not commit passwords, access tokens, private camera recordings, or files containing personal data.

## Privacy and security

Camera frames are processed locally by OpenCV and MediaPipe. The project does not need to upload video to a cloud service. Camera access is still sensitive: grant it only to trusted users and keep the repository free of recordings. System and power actions can affect unsaved work, so review every rule before enabling automatic detection.

## Possible future improvements

- A camera selector in the settings window.
- A reconnect-camera button and a live camera diagnostic panel.
- Automated tests for gesture classification and action dispatch.
- User profiles for different applications and games.
- Configurable confidence, smoothing, cooldown, and sensitivity thresholds.
- A preview showing the detected gesture before an action is sent.
- Confirmation dialogs for shutdown, restart, and other destructive actions.
- Improved performance on low-end computers and faster mask rendering.
- A signed Windows installer and automatic updates.
- Linux and macOS support where the required input APIs are available.
- A plugin system for custom gestures and user-defined actions.

## License

Choose a license before publishing the repository. MIT is a simple permissive choice for a small open-source utility: it allows reuse and modification as long as the copyright and license notice are retained. Apache-2.0 is a good alternative when an explicit patent grant is important. GPLv3 is appropriate when you want modified distributed versions to remain open source. Also review the licenses of all third-party dependencies before distributing a packaged application.

## Reporting issues

When reporting a problem, include the Windows version, Python version, TechGest version or commit, camera model, connected displays, steps to reproduce the issue, the selected gesture rule, and the relevant section of `techgest.log`. Never include passwords, tokens, private recordings, or other sensitive data.
