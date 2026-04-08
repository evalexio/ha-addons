# HAPiyoAI

Connects Home Assistant to a [PiyoAI](https://github.com/alexsoh/piyoai) instance.

## Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `piyoai_url` | Base URL of your PiyoAI service | `http://192.168.1.100` |
| `piyoai_port` | Port PiyoAI is running on | `8000` |

## Home Assistant First-Time Install

If you have never installed a custom add-on repository before, follow this exactly.

### 1) Add this repository to HA

1. Open Home Assistant in your browser.
2. Click **Settings** in the left sidebar.
3. Click **Add-ons**.
4. Click **Add-on Store**.
5. Click the **three dots (⋮)** in the top-right.
6. Click **Repositories**.
7. Paste this URL:
   - `https://github.com/evalexio/ha-addons`
8. Click **Add**.
9. Close the dialog.

### 2) Install hapiyoai

1. In Add-on Store search, type `hapiyoai`.
2. Open the `hapiyoai` add-on page.
3. Click **Install** and wait for completion.

### 3) Configure and start

1. Open the **Configuration** tab.
2. Set:
   - `piyoai_url` (example: `http://192.168.1.50`)
   - `piyoai_port` (usually `8000`)
3. Click **Save**.
4. Open the **Info** tab.
5. Click **Start**.
6. Wait for the add-on to finish starting.
7. Click **Open Web UI**.

## Setup

1. Configure `piyoai_url` and `piyoai_port` in the add-on options
2. Start the add-on
3. Open the ingress UI (click **Open Web UI**)
4. Go to the **Setup** page
5. Enter your desired topic prefix (e.g. `hapiyoai`)
6. Set the global idle timeout (seconds before a camera reverts from Detected to Idle)
7. Click **Discover** — PiyoAI cameras will appear with checkboxes
8. Select the cameras you want to monitor; optionally expand **Advanced** per camera to set a custom idle timeout
9. Click **Install**

## Install and Test Checklist

Use this after first install or after upgrade.

### 1) Basic connectivity

1. Open the add-on logs and confirm startup is healthy (no repeated tracebacks).
2. In the UI, click **Discover**.
3. Verify cameras are returned and MQTT broker info is shown.

If Discover fails, validate:
- `piyoai_url` and `piyoai_port` are correct
- PiyoAI is reachable from Home Assistant
- PiyoAI has MQTT configured

### 2) Install flow

1. Set a `topicPrefix` (for example `hapiyoai`).
2. Set global idle timeout (for example `30` seconds).
3. Select one or more cameras and click **Install**.
4. Confirm selected cameras appear on the Dashboard.

### 3) Detection + idle behavior

1. Trigger a known detection on one selected camera.
2. Verify dashboard card updates with latest image.
3. Verify camera badge changes to **Detected**.
4. Wait for timeout period and verify badge returns to **Idle**.

### 4) Home Assistant entity verification

For each installed camera, check entity state in HA:

- Entity id: `binary_sensor.hapiyoai_{camera_id_slug}`
- `on` means recently detected
- `off` means idle
- Attributes should include:
  - `friendly_name`
  - `last_image`
  - `detected_objects`

### 5) Reconfigure scenarios

1. Change `topicPrefix`, click **Install** again, verify images continue arriving.
2. Deselect a camera and click **Install**:
   - camera is removed from dashboard
   - HA state is reset to idle
3. Set per-camera timeout override in **Advanced**, verify override applies.

### 6) Error-handling checks

- Invalid per-camera timeout (<5 or >3600) should return `400`.
- Selecting unknown camera ids should return `400`.
- If PiyoAI returns invalid MQTT port data, install should fail with a clear error.
- If PiyoAI is unreachable, API should return `502`.

### 7) Restart persistence

1. Restart the add-on.
2. Verify previously installed cameras are still listed.
3. Verify dashboard continues to update on new images.

## Reconfigure

To add/remove cameras or change the topic prefix, go to the **Setup** page and click **Reconfigure**. This re-discovers cameras, applies setup-mqtt for selected cameras, and calls cleanup for deselected ones.

## Home Assistant Entities

Each configured camera creates a `binary_sensor.hapiyoai_{camera_id}` entity with:
- `device_class: motion`
- `state: on` — image received within the idle timeout window
- `state: off` — idle (no recent image)
- Attributes: `friendly_name`, `last_image` (ISO timestamp), `detected_objects` (list)
