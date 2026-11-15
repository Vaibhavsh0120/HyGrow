# Two proposed research-paper abstracts for HyGrow

Prepared 14 November 2026 for a 1–2 week evaluation using the team's working hardware and plants.

These are proposal-stage abstracts. The experiments and additions described below are planned, not completed. Add measured findings before submitting completed empirical papers. The full literature comparison, six other ideas with abstracts, and experiment plans are in [the research report](HYGROW_RESEARCH_OPPORTUNITIES_2026-11-14.md).

## Paper 1

### Freshness-Aware Local–Cloud Telemetry for Reliable Hydroponic Monitoring: Design and Fault-Injection Evaluation of HyGrow

Reliable hydroponic monitoring requires distinguishing a reachable device from a fresh and valid sensor measurement. A successfully delivered response can contain an old, failed, simulated, or previously observed sample, limiting the value of connectivity alone as an indicator of monitoring reliability. This paper proposes an experimental evaluation of HyGrow, an ESP32-S3 hydroponic monitoring platform with local and cloud telemetry paths. Its telemetry contract associates measurements with hardware identity, boot identity, sample sequence, sample age, and sensor-health metadata. The study will compare cloud-only, local-only, and combined transport configurations, alongside an ablation that omits quality checks. Controlled experiments will introduce internet outages, local-path failures, device reboots, sensor-task stalls, and delayed or duplicated observations. Evaluation will measure fresh-data availability, stale-data acceptance, observation duplication, recovery time, and alert validity using independently recorded fault events. An extended physical deployment will assess monitoring continuity under ordinary operating conditions. The intended contribution is a reproducible framework for evaluating measurement validity across transport failures, rather than relying exclusively on message delivery or device online status. The study will establish the benefits and limitations of applying explicit telemetry provenance and freshness policies to low-cost hydroponic monitoring.

**Keywords:** hydroponics; ESP32-S3; telemetry; data freshness; fault injection; edge–cloud monitoring.

## Paper 2

### Capture-Condition Robustness and Selective Prediction in Lightweight Plant Disease Classification: A HyGrow Case Study

Plant disease classifiers trained on controlled leaf-image datasets may produce confident errors when photographs differ in illumination, background, or image quality. This paper proposes a deployment-oriented evaluation of the MobileNetV3-Large classifier used in HyGrow, covering fifteen leaf-condition classes across bell pepper, potato, and tomato. The study will combine a leaf-grouped PlantVillage evaluation, a label-aligned external benchmark, and an original paired-image dataset collected under predefined capture conditions in the team's growing setup. The evaluation will compare forced classification, temperature-scaled predictions, confidence-based abstention, and a quality-aware policy that permits one predefined recapture before deferring uncertain cases. Calibration parameters and decision thresholds will be selected using data separated from final testing by physical leaf or source image. Performance will be assessed using macro-F1, calibration error, Brier score, accepted-prediction error, coverage, unknown-input acceptance, and recapture cost. Locally collected healthy-leaf evidence will be distinguished from externally labelled disease-image results. The intended contribution is a reproducible capture-condition benchmark and an assessment of when a lightweight classifier should answer, request another image, or defer. The study will characterize practical reliability limits without assuming that high controlled-dataset accuracy establishes hydroponic disease-diagnosis performance.

**Keywords:** plant disease classification; MobileNetV3; domain shift; confidence calibration; selective prediction; capture robustness.
