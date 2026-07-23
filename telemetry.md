# F1 Race Replay – Telemetry Stream Demo

This guide explains how to use the experimental telemetry streaming feature. It exposes live telemetry data generated during replay, allowing developers to build custom dashboards, overlays, or other tools that consume replay data in real time.



## Getting Started

Before using telemetry streaming, make sure you have:

- Installed the latest version of the application with telemetry support.
- Completed the project setup and installed all dependencies (refer to the main `README.md`).

### 1. Launch with telemetry enabled

```bash
python main.py --telemetry
```

### 2. Start a replay

Open the application, select a race session from the GUI, and begin playback. The telemetry server will automatically start streaming data.

### 3. Explore the demo viewer

The demo interface includes several views:

- **Raw Telemetry** – Live JSON messages received from the replay engine.
- **Summary** – Current session information, connection status, and message statistics.
- **Drivers** – Driver positions, speed, lap data, and other live telemetry.
- **Events** – Track status updates and race events as they occur.

---

## Why Telemetry Streaming?

One of the long-term goals of this project is to create a true **virtual pit wall** for Formula 1 fans.

Streaming telemetry opens the door to much more than the built-in replay window. Developers can build completely separate applications that consume live replay data, including:

- Custom dashboards
- Telemetry visualizations
- Strategy analysis tools
- Stream overlays
- Companion applications

Instead of squeezing every feature into a single interface, telemetry streaming makes it possible to create specialized tools that work alongside the replay and provide a far richer analysis experience.

---

## Telemetry Data Format

Each telemetry update is sent as a JSON object.

```json
{
  "frame": {
    "drivers": {
      "ALB": {
        "brake": 0.0,
        "dist": 4428.078064476834,
        "drs": 1,
        "gear": 7,
        "lap": 1,
        "position": 8,
        "rel_dist": 0.7588,
        "speed": 282.0,
        "throttle": 100.0,
        "tyre": 3.0,
        "x": 5303.823151332268,
        "y": 554.943926757651
      },
      "... other drivers"
    },
    "safety_car": {
      "x": 3456.78,
      "y": 1234.56,
      "phase": "on_track",
      "alpha": 1.0
    },
    "lap": 1,
    "t": 84.88,
    "weather": {
      "air_temp": 18.34674710038636,
      "humidity": 80.46747100386358,
      "rain_state": "DRY",
      "track_temp": 24.19349420077272,
      "wind_direction": 201.09865351318817,
      "wind_speed": 1.1532528996136413
    }
  },
  "frame_index": 2122,
  "is_paused": true,
  "playback_speed": 64.0,
  "session_data": {
    "lap": 1,
    "leader": "VER",
    "time": "00:01:24",
    "total_laps": 52
  },
  "total_frames": 148011,
  "track_status": "2"
}
```

### Safety Car Data

The `safety_car` object contains simulated Safety Car telemetry and is `null` whenever the Safety Car is not deployed.

| Field | Type | Description |
|-------|------|-------------|
| `x` | float | World X coordinate |
| `y` | float | World Y coordinate |
| `phase` | string | Animation state (`deploying`, `on_track`, `returning`) |
| `alpha` | float | Opacity value (`0.0`–`1.0`) used for fade animations |

> **Note:** Since the official F1 telemetry does not expose the Safety Car's GPS position, its location is simulated (approximately 500 m ahead of the race leader). The `phase` field can be used by external applications to trigger animations or UI effects.

---

## Technical Details

| Property | Value |
|----------|-------|
| **Protocol** | TCP |
| **Address** | `localhost:9999` |
| **Message Format** | Newline-delimited JSON |
| **Networking** | Background thread |
| **GUI Framework** | PySide6 (Qt for Python) |
