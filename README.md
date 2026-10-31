# HyGrow

**Hydroponics monitoring, farm management and plant insights.**

ESP32-S3 sensors, a mobile/web app and an API, with a separate plant disease research project.

## Run locally

Requires **Node.js 22 LTS (22.13+)**. From this folder:

```powershell
cd HyGrow_Software
npm run install-all
npm start
```

Open the web address printed by Expo. The API runs on **port 3000**.
Stop both with **Ctrl+C**. On later runs, use only `npm start`.

> First setup? Follow the [software setup guide](HyGrow_Software/README.md#first-time-setup)
> to configure the environment files. Keep existing `.env` files; encrypted
> `.env.age` backups are tracked, while plaintext secrets stay local.

## Project folders

| Folder | Contains | Guide |
| --- | --- | --- |
| [HyGrow_Software](HyGrow_Software/) | Expo app, Express API and Firebase Function | [Setup and commands](HyGrow_Software/README.md) |
| [HyGrow_IoT](HyGrow_IoT/) | ESP32-S3 firmware, sensors and local dashboard | [Wiring and flashing](HyGrow_IoT/README.md) |
| [HyGrow_AI](HyGrow_AI/) | Plant disease model training and inference | [AI setup](HyGrow_AI/README.md) |
| [HyGrow_Docs](HyGrow_Docs/) | Presentations and project documents | [Open folder](HyGrow_Docs/) |

## Get the complete project

```sh
git clone --recurse-submodules https://github.com/Vaibhavsh0120/HyGrow.git
```

Software, IoT and AI are Git submodules. Push component commits first, then the
parent commit that references them.

## Connect a device

Set `EXPO_PUBLIC_DEVICE_ID` to the firmware's device ID. The app reads the selected
`devices/{deviceId}` document. Optional local HTTP is explained in the
[telemetry guide](HyGrow_Software/FrontEnd/docs/TELEMETRY.md).
Unavailable and simulated readings are labelled. Charts use samples observed
in the current session. Pump control is not implemented by this firmware.

> A phone's `localhost` is the phone itself. Use your computer's LAN IP during
> development and keep both devices on reachable Wi-Fi. College/guest Wi-Fi can
> isolate devices; a personal hotspot may be needed. Release APIs use HTTPS.

## Review notes

[Code review results](docs/final-review.md) record verified checks, remaining
upstream dependency findings and Firebase/device release requirements.
