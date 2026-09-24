# HyGrow

HyGrow combines an ESP32-S3 hydroponics monitor, a mobile and web farm management app, an API, and plant disease research. This parent repository contains three Git submodules and the project documents. Each component has its own setup and history.

## Repository layout

| Folder | Repository content | Start here |
| --- | --- | --- |
| [`HyGrow_IoT`](HyGrow_IoT/) | ESP32-S3 firmware, six sensor modules, local dashboard, and optional Firestore upload | [IoT guide](HyGrow_IoT/README.md) |
| [`HyGrow_Software`](HyGrow_Software/) | Expo/React Native app, Express API, Firebase Function, and mobile release workflow | [Software guide](HyGrow_Software/README.md) |
| [`HyGrow_AI`](HyGrow_AI/) | PlantVillage datasets, fifteen-class MobileNetV3 training, ONNX export, and a local Gradio demo | [AI guide](HyGrow_AI/README.md) |
| [`HyGrow_Docs`](HyGrow_Docs/) | Synopsis and presentation files tracked directly in this parent repository | Open the files in the folder |

The component folders are existing Git repositories. Their remotes and pinned commits are declared in [`.gitmodules`](.gitmodules); there is no missing repository to initialize.

## How the components currently connect

```text
ESP32-S3 sensors → board-hosted LittleFS dashboard
                 → optional Firestore devices/{device_id}

Expo app         → Firebase Auth and Firestore app collections
                 → devices/{device_id} when EXPO_PUBLIC_DEVICE_ID is set
                 → Express API → hosted Gradio disease/growth services

HyGrow_AI        → local training and Gradio demo
```

The firmware writes its **current device state** to `devices/{device_id}`. Set `EXPO_PUBLIC_DEVICE_ID` to that device ID for the app to subscribe to it. Without this setting, the app retains its older `sensor_readings` subscription. The firmware document contains current state only, so it does not supply historical charts. A separate history writer is needed for trends. The `demoMode` field identifies devices with simulated sensor readings. The app has actuator UI and bridge code, but the firmware has no implemented pump or relay control loop; the dashboard labels that integration as unavailable.

The AI submodule trains and exports the disease model and has a local ONNX Gradio demo. The Express backend calls the Gradio endpoint selected by `HF_SPACE`, which can be a local URL or a deployed Hugging Face Space running `HyGrow_AI/hydro-disease/app.py`. The Firebase Function still targets a separately configured hosted disease Space and is not used by the current app disease screen. The growth Space is an external model separate from this AI submodule. Farm chat calls the Express backend with a Firebase ID token; its Gemini provider key stays on the backend.

## Clone

The AI submodule tracks model artifacts, while its larger training dataset is downloaded separately by its setup script.

```sh
git clone --recurse-submodules https://github.com/Vaibhavsh0120/HyGrow.git
cd HyGrow
```

If the parent repository is already cloned:

```sh
git submodule update --init --recursive
```

Run `git submodule status` to confirm all three components are present. A `-` prefix means a submodule has not been initialized.

## Run the software

Use Node.js 22 for the root command runner, frontend, and Express API. Install from each lockfile in `HyGrow_Software`:

```sh
cd HyGrow_Software
npm ci
npm --prefix FrontEnd ci
npm --prefix BackEnd ci
npm run dev
```

The API listens on port 3000 by default. Expo prints the frontend URL and device instructions. [`FrontEnd/.env.example`](HyGrow_Software/FrontEnd/.env.example) shows the optional backend URL and device ID settings; use your computer's LAN address for a physical phone. To run disease prediction locally, start the AI Gradio demo and set `HF_SPACE=http://127.0.0.1:7860` in `BackEnd/.env`. Firebase access, growth prediction, email, and payments each require their corresponding external service configuration. The Firebase Function is a separate package under `FrontEnd/functions` and declares Node.js 24.

See the [frontend](HyGrow_Software/FrontEnd/README.md) and [backend](HyGrow_Software/BackEnd/README.md) guides for their screens and API endpoints. The frontend uses Expo SDK 57 and React Native 0.86.

## Build the IoT device

The firmware targets an ESP32-S3 N16R8. Copy `HyGrow_IoT/example.secrets.h` to the ignored `HyGrow_IoT/secrets.h` and fill in the first-boot settings; compilation intentionally fails without that file. Flash both the firmware and the `data/` LittleFS image, which contains the local dashboard. PlatformIO configuration is in [`platformio.ini`](HyGrow_IoT/platformio.ini). Follow the [IoT guide](HyGrow_IoT/README.md) for wiring, calibration, upload steps, and optional Firestore provisioning.

The local dashboard works without cloud access. Firestore sync is optional and writes one document per device. The firmware does not itself mark an offline device as offline; a reader must detect stale `lastUpdated` timestamps.

## Explore the AI model

The [AI guide](HyGrow_AI/README.md) explains the pinned PlantVillage download and preparation command, training, evaluation, ONNX export, and local Gradio demo. The large source and generated datasets are ignored by Git. The inference demo runs from `HyGrow_AI/hydro-disease` with the checked-in ONNX model and `class_names.json`. Its `requirements.txt` covers inference; `requirements-train.txt` lists the additional training and export packages. Retraining and deploying a newly exported model are separate steps.

## Mobile release workflow

The software repository's [Build Mobile Releases (Unsigned)](HyGrow_Software/.github/workflows/build-release-unsigned.yml) workflow is manually dispatched in **the software repository**. Set its repository variables `EXPO_PUBLIC_BACKEND_URL` (deployed HTTPS Express API) and `EXPO_PUBLIC_DEVICE_ID` (the firmware document ID) before dispatching a connected build. It builds an Android release APK signed with the project's debug key and an unsigned iOS IPA, then publishes both only if both build jobs pass. The unsigned IPA needs signing before it can run on an iPhone.

The iOS job requires a macOS runner. Windows installation and web export do not verify Xcode compilation. If an iOS run fails, inspect the first compiler or script error in the `xcodebuild-archive-log` artifact or the workflow summary; a later missing `.app` message can be downstream of that error. Android and iOS native projects are regenerated by the workflow from the app config.

## Working with submodules

Commit a change inside its component repository first. Then, in the parent repository, commit the updated submodule pointer along with any parent documentation changes. `HyGrow_Docs` is an ordinary parent directory, so its file changes are committed directly in the parent.

Local `.env` files, device secrets, and generated files should stay out of Git. An ngrok token previously committed in the software start script was removed from the current file; rotate that token because Git history still contains it.
