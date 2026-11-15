# HyGrow Paper 1 bench tests

On 15 November 2026, we ran three simulated browser internet outages, three ESP32 restarts, and three TDS module disconnections on one HyGrow prototype. The bench setup used a DHT22 and a TDS probe in a glass of tap water for the selected measurements.

| Scenario | Runs | Observation |
| --- | ---: | --- |
| Simulated browser internet outage | 3 | The dashboard selected 18 distinct LAN samples per run. The browser received zero Firestore telemetry documents during each block. After access returned, the first newer Firestore documents arrived in 1.9, 7.8, and 2.2 s. |
| Physical ESP32 restart | 3 | A new boot ID was observed in every run. Fresh air-temperature readings returned to the dashboard approximately 8.9, 1.7, and 4.9 s after the operator markers. A stale or ingestion-error label was captured in one run. |
| Physical TDS module disconnection | 3 | The ESP32 stayed powered and continued publishing numeric readings with a `healthy` TDS flag. The browser captures showed no explicit stale, unavailable, or offline label for TDS. Values during the disconnection windows ranged from about 62 to 917 ppm across the three runs, compared with the sampled pre-fault readings of about 122–128 ppm. |

## Setup and measurements

- One ESP32-S3 on firmware `3.1.4` with telemetry schema `1` and a reported sampling interval of 2 s. The firmware repository was at `0c8330e`. Air temperature from the DHT22 was the reference channel for the outage and restart runs. The removable TDS module was the target for the sensor runs.
- The HyGrow grower dashboard ran locally on Microsoft Edge from software commit `eab4d78` with the source badge repair applied. That repair displayed the source of the selected sample. The later visual and graph updates were verified in a separate preview.
- Browser internet loss was simulated by closing and blocking the test browser's external proxy connections for about 35 s while allowing direct HTTP to the ESP32. The router and ESP32 remained online. The dashboard stayed in combined local-and-cloud monitoring. Recovery denotes the first newer Firestore telemetry document observed by the browser after the block was removed.
- Restart trials used a momentary press of the ESP32 reset button. Sensor trials disconnected and reconnected the TDS module while leaving the board powered. The operator entered a marker immediately after each physical action. Physical recovery times are approximate and include the operator's marking delay and observer polling resolution.
- The laptop recorded the ESP32 `/status` payload about once per second and timestamped the browser observations about twice per second. Original USB serial output was captured alongside the board samples. UTC timestamps and the boot ID and sample sequence fields align the records, including across restarts. A `10 s` freshness threshold applied at the configured 2 s firmware sampling interval.
- None of the 715 completed ESP32 payloads captured across the nine trials reported a simulated sensor. The TDS values during disconnection came from the physical board's analog input.
- TDS recovery in the workbook is the first observed post-reconnect value within ±10% of that run's pre-fault reading, an exploratory analysis threshold. The displayed numerical values demonstrate the disconnection behavior observed on this setup. The water sample had no calibrated reference measurement.

## Final protocol and actual trial timing

We used the app's freshness rule as configured before the formal trials. A sample was stale when its estimated age exceeded the greater of 10 s or three sampling intervals. The firmware sampled every 2 s, so the fixed limit was 10 s for all nine trials. The 10 s floor gave the transport time to deliver routine samples, while the three-interval rule allows for boards configured to sample less often.

The observation window runs from the browser capture start to capture stop. Repeat spacing is the time between consecutive fault onsets within a scenario. After-fault time is measured from restoration for the network and TDS runs, and from the reset marker for the boot runs. All durations are in seconds.

| Run | Total window | Before fault | Fault duration | After fault or reset | Spacing from prior trial |
| --- | ---: | ---: | ---: | ---: | ---: |
| NET-01 | 93.1 | 18.0 | 35.1 | 40.1 | — |
| NET-02 | 88.7 | 16.5 | 35.0 | 37.2 | 94.2 |
| NET-03 | 89.0 | 16.5 | 35.0 | 37.4 | 91.4 |
| BOOT-01 | 66.8 | 23.4 | Momentary press | 43.4 | — |
| BOOT-02 | 61.2 | 21.8 | Momentary press | 39.4 | 67.8 |
| BOOT-03 | 57.7 | 18.0 | Momentary press | 39.7 | 59.5 |
| SENSOR-01 | 84.2 | 24.0 | 35.6 | 24.7 | — |
| SENSOR-02 | 79.0 | 29.0 | 26.1 | 23.8 | 91.7 |
| SENSOR-03 | 89.1 | 36.9 | 28.7 | 23.5 | 88.9 |

The formal outages blocked browser internet access while keeping both the ESP32 and laptop connected to the LAN. The hardware operator chose the removable TDS module for this test. The operator marked physical actions immediately afterward, and the interruption durations varied because of the manual reconnect timings. The practice network outage used a different browser blocking method and was excluded. The unsuccessful USB reset attempt, water-temperature setup capture, and later 3.1.5 repair validation were also excluded from the nine formal runs.

## Evidence

The local handoff folder is `HyGrow_Docs/output/paper-1/`. It contains the workbook, per-run ESP32 and browser logs, the fault markers, and one dashboard recording per scenario. The practice outage and unsuccessful USB reset attempt are in the pilot folder and excluded from the table.

## Follow-up on firmware 3.1.5

Firmware 3.1.5 tests the TDS analog input with weak pull resistors and releases both pulls before measuring ppm. In a separate physical check, the connected module reported healthy readings, the disconnected input became `failing` with `tds_ppm: null`, and the module returned to healthy readings after reconnection. The ESP32 boot ID stayed the same and the DHT22 kept reporting. The local `follow-up/` folder holds the capture. The nine workbook runs above retain the original 3.1.4 behavior.
