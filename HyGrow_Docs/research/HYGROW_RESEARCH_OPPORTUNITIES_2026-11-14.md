# HyGrow: research opportunities, literature assessment, and proposed abstracts

Research date: 14 November 2026. Scope: the current local HyGrow implementation, a working ESP32 with sensors and plants, and 1–2 weeks of additional work.

## Recommendation

Develop two separate empirical papers:

1. **Freshness-Aware Local–Cloud Telemetry for Reliable Hydroponic Monitoring: Design and Fault-Injection Evaluation of HyGrow.** This uses the existing firmware and app, with measurement logging and controlled failure experiments.
2. **Capture-Condition Robustness and Selective Prediction in Lightweight Plant Disease Classification: A HyGrow Case Study.** This uses the existing classifier, an original paired-image evaluation, and small calibration/abstention additions.

These are plausible systems and applied machine-learning studies, not established new algorithms. Their strongest prospective contributions are original experimental evidence, a reproducible protocol, and reusable evaluation data. Acceptance and global originality cannot be established by this search. Changing a project name, microcontroller, or model backbone does not establish novelty.

If appropriately labelled image data cannot be obtained, replace Paper 2 with **Dependency-Aware TDS Monitoring under Water-Temperature Sensor Failure** (Idea 3), provided a calibrated reference meter and standards can be borrowed. Do not promise a hydroponic disease benchmark using only healthy plants.

Every abstract below is a **proposal-stage draft**. Proposed methods and evaluations have not been implemented or executed in this research task. No experimental results are invented. For an empirical paper submission, complete the experiments and add actual sample sizes, numerical findings, and limitations.

## What is actually in the project

The evidence below comes from source inspection, not from physical verification during this task.

| Component | Existing evidence | Research implication |
| --- | --- | --- |
| ESP32-S3 sensing | Separate FreeRTOS sensor/network tasks; DHT22, DS18B20, BH1750, water level, TDS and optional pH | Suitable for acquisition-continuity and network-interference experiments |
| Canonical telemetry | Hardware ID, boot ID, sample sequence, sample age, read interval, health and simulation metadata | Supports tests of stale readings, duplicate observations and reboot boundaries |
| Local and cloud access | Local `/status`, on-device dashboard, optional Firestore uploads; app prefers fresh local observations | Supports direct comparison of local-only, cloud-only and combined monitoring |
| Data-quality handling | Missing values stay null; simulated readings labelled; stale or unsuitable samples excluded from alerts | Valuable implementation feature; requires measured evaluation before claiming effectiveness |
| TDS dependency | Median filtering, calibration factor, measured water-temperature compensation, explicit 25 °C fallback flag | Offers a narrowly scoped measurement-reliability study |
| Image model | MobileNetV3-Large, 15 pepper/potato/tomato classes, PyTorch training, ONNX export, Gradio inference | Existing backbone and pipeline can be reused without inventing a new network |
| Dataset handling | PlantVillage revision recorded; physical-leaf grouping in preparation | Good reproducibility practice, but leaf-grouped splitting already exists in prior work |
| Evaluation tooling | Accuracy, macro-F1, per-class metrics and confusion matrix | Calibration, unknown-input tests and capture-condition evaluation would be additions |

Important boundaries:

- Pump actuation and irrigation automation are explicitly unimplemented. Actuator UI commands reject as unavailable.
- TDS is provided in ppm. EC is unsupported in the app; no undocumented conversion should be presented as a direct EC measurement.
- App charts contain at most 100 observed session samples. A persistent research archive and board-side store-and-forward queue are additional work.
- The classifier runs through an external/server inference path. The existing model is not demonstrated running on the ESP32.
- Existing checkpoint/ONNX artifacts predate the leaf-grouped preparation pipeline. Retraining and reevaluation are needed before attributing results to that pipeline.
- The inspected [project synopsis](../../HyGrow_Docs/Synopsis%20MProject.pdf) describes closed-loop regulation, EC and biomass benefits beyond the current implementation. Treat these as earlier ambitions, not established paper results.

Owning local sources: [root README](../../README.md), [firmware README](../../HyGrow_IoT/README.md), [telemetry schema](../../HyGrow_IoT/src/core/telemetry.h), [telemetry serialization](../../HyGrow_IoT/src/core/telemetry_json.cpp), [sensor acquisition](../../HyGrow_IoT/src/core/task_sensor.cpp), [TDS implementation](../../HyGrow_IoT/src/sensors/sensor_tds.cpp), [app telemetry guide](../../HyGrow_Software/FrontEnd/docs/TELEMETRY.md), [normalization and eligibility rules](../../HyGrow_Software/FrontEnd/src/utils/telemetry.ts), [transport selection](../../HyGrow_Software/FrontEnd/src/hooks/useSensorData.ts), [AI README](../../HyGrow_AI/README.md), [training](../../HyGrow_AI/hydro-disease/train.py), [preparation](../../HyGrow_AI/hydro-disease/prepare_data.py), [evaluation](../../HyGrow_AI/hydro-disease/evaluate.py), and [inference](../../HyGrow_AI/hydro-disease/app.py).

## Literature: what is already done

This is a targeted literature assessment, not a systematic review or exhaustive novelty search. Searches covered hydroponic IoT, local/cloud synchronization, sensor faults, temperature compensation, plant disease domain shift, calibration and model compression. Full-text methods/results were examined where accessible; abstract-only and indexed-excerpt evidence is identified explicitly. Publisher and author sources were preferred.

| Ref. | Verified publication | Evidence examined and implication for HyGrow |
| --- | --- | --- |
| R1 | Sportelli et al. (2025), *Beyond Prototypes: What Is Missing to Fill the Gaps in IoT-Enabled Hydroponics Platforms*, Horticulturae 11(11), 1322. [Paper](https://doi.org/10.3390/horticulturae11111322) | Full-text methodology and review findings. Reviews 74 studies and identifies fragmentation and maturity problems. Supports reproducibility/interoperability research; contradicts a broad claim that hydroponic IoT integration itself is new. |
| R2 | Wardihani et al. (2025), *Synchronization of Data Transmission between Edge and Cloud Network in IoT-Based Hydroponic Systems*, IJASEIT 15(2), 601–609. [Paper](https://doi.org/10.18517/ijaseit.15.2.20326) | Publisher abstract and indexed excerpts; repeated full-text requests were blocked. Edge/cloud synchronization during interruptions is prior work. Do not claim offline operation or reconnection alone as novel. |
| R3 | Demir and Çiçek (2026), *A Low-Cost and Reliable IoT-Based NFT Hydroponics System Using ESP32 and MING Stack*, Engineering Proceedings 122(1), 3. [Full text](https://www.mdpi.com/2673-4591/122/1/3) | Full-text architecture, sensing, logging, alarms and evaluation. Already combines ESP32, several similar sensors, calibration, a mobile app, storage and actuation. Its evaluated excerpt does not establish HyGrow's proposed specific failure matrix. |
| R4 | Castro García et al. (2025), *I-Canopy: A resilient IoT platform for adaptive environmental monitoring at the edge*, Smart Agricultural Technology 12, 101611. [Paper](https://doi.org/10.1016/j.atech.2025.101611), [author-hosted PDF](https://arroma.uiowa.edu/docs/publication/paper_pdf/2025/castro_et_al_2025.pdf) | Indexed author-paper excerpts included architecture, firmware and field evaluation; direct PDF fetch timed out. ESP32 local storage, adaptive operation and later synchronization already exist in agricultural monitoring. |
| R5 | Ferentinos, Albright and Selman (2003), *Neural network-based detection of mechanical, sensor and biological faults in deep-trough hydroponics*, Computers and Electronics in Agriculture 40, 65–85. [Author-hosted full text](https://www.cs.cornell.edu/selman/papers/pdf/03.comp-elec-agri.neural.pdf) | Full-text experimental setup and fault modelling. Sensor and biological fault detection in hydroponics has a long history; it is not a new research category. |
| R6 | Karimzadeh, Li and Ahamed (2025), *Machine learning-based fault detection and diagnosis of electrical conductivity and pH sensors in hydroponic systems*, Computers and Electronics in Agriculture 237, 110544. [Publication](https://doi.org/10.1016/j.compag.2025.110544), [author-affiliated record and abstract](https://research.aalto.fi/en/publications/machine-learning-based-fault-detection-and-diagnosis-of-electrica/) | Published metadata and abstract, not full methods. Compares five ML models for five fault types and includes sensor-independent detection. A generic random-forest fault detector would substantially overlap existing work. |
| R7 | Mohanty, Hughes and Salathé (2016), *Using Deep Learning for Image-Based Plant Disease Detection*, Frontiers in Plant Science 7, 1419. [Full text](https://www.frontiersin.org/journals/plant-science/articles/10.3389/fpls.2016.01419/full) | Full-text dataset, split protocol and experiments. PlantVillage classification and grouping multiple views of the same physical leaf are established practices. HyGrow cannot claim either as newly invented. |
| R8 | Singh et al. (2020; preprint 2019), *PlantDoc: A Dataset for Visual Plant Disease Detection*, CoDS-COMAD, 249–253. [Publication](https://doi.org/10.1145/3371158.3371196), [full text](https://arxiv.org/html/1911.10317) | Full-text curation, classification and detection experiments. Demonstrates limitations of controlled-image training on more varied images. Its web-sourced labels are useful benchmark annotations, not laboratory confirmation of disease. |
| R9 | Guo et al. (2017), *On Calibration of Modern Neural Networks*, ICML/PMLR 70, 1321–1330. [Paper and PDF](https://proceedings.mlr.press/v70/guo17a.html) | Full-text calibration methods and comparative results. Temperature scaling is a standard post-processing baseline, not a novel HyGrow method. |
| R10 | Xiang, Shi and Zhu (2026), *Quantifying the reliability gap in cross-domain plant disease classification: benchmarking the limited efficacy of standard mitigation techniques under controlled-to-field shift*, Frontiers in Plant Science 17, 1826962. [Full text](https://www.frontiersin.org/journals/plant-science/articles/10.3389/fpls.2026.1826962/full) | Full-text category alignment, grouped target partitions, mitigation methods and results. Already studies calibration, selective prediction and unknown rejection across PlantVillage/PlantDoc. HyGrow needs an original capture-condition study or another substantive extension; a MobileNet substitution alone is weak. |
| R11 | Oliveira et al. (2024), *Calibration and Accuracy of TDS Reader in Low-Cost Hydroponic System*, Irriga 29, 134–140. [Publication](https://doi.org/10.15809/irriga.2024v29p134-140) | Indexed publisher abstract/metadata; publisher site was undergoing maintenance. Calibration improvement alone is prior work. Idea 3 focuses on failed temperature dependencies and decision availability. |
| R12 | Sopingi, Pradana, Jolanda and Iman (2026), *Diagnosis and Mitigation of INT8 Quantization Failure in MobileNetV3 for Plant Leaf Disease Classification*, ICOHETECH proceedings, published 26 September 2026. [Publisher record](https://ojs.udb.ac.id/icohetech/article/view/7127) | Publisher abstract and metadata, not full methods. A close existing study of MobileNetV3 plant classification and INT8 failure. Idea 6 is a fallback deployment benchmark, not a strong new algorithm claim. |

### What could distinguish HyGrow

The following are **candidate contributions inferred from the code and the reviewed literature**, not proof that no similar work exists:

1. An end-to-end failure benchmark that separates network reachability from measurement freshness, sensor health and simulation status, while tracking sample identity across local/cloud paths and reboots.
2. Original paired images from the team's growing setup, with controlled capture conditions and an explicitly evaluated recapture/abstention policy for the deployed classifier.
3. A measurement study of how loss of water-temperature sensing changes TDS error, threshold decisions and usable-data availability.

The original measurements and reproducible comparisons are essential. A list of implemented features is not enough.

## Eight practical paper directions

Effort estimates are planning judgments for additional work, not guarantees. Experiments can run concurrently across the team. The options are alternatives: several overlap and should not become separate papers using the same contribution.

| Idea | Direction | Additional work | Differentiation potential | Recommendation |
| --- | --- | --- | --- | --- |
| 1 | Freshness-aware telemetry under failures | About 2–4 days instrumentation, then repeated trials and a 3–7 day soak | Good systems case study if the failure evidence is original | First paper |
| 2 | Capture conditions and selective prediction | About 4–7 days code/data preparation, plus training and evaluation | Moderate; original capture data is essential because R10 is close | Second paper |
| 3 | TDS compensation dependency failures | About 2–4 days logging/protocol, then repeated bench trials | Moderate applied measurement contribution; needs a reference instrument | Best replacement for 2 |
| 4 | Lightweight sensor fault diagnosis | About 5–8 days plus time-series collection | Crowded; embedded overhead and realistic event evaluation must distinguish it | Alternative |
| 5 | Event-triggered cloud uploads | About 3–5 days changes, then trace replay and device tests | Modest; quantify information loss as well as message savings | Alternative |
| 6 | Compression versus prediction reliability | About 4–7 days benchmarking; longer for native phone integration | Crowded; R12 is particularly close | Alternative, often better inside 2 |
| 7 | Bounded offline telemetry archive | About 5–8 days firmware/storage changes and recovery testing | Modest; buffer correctness and explicit overflow policy can contribute | Alternative, often better inside 1 |
| 8 | Image-plus-environment triage | Likely beyond 1–2 weeks unless paired expert labels already exist | Potentially stronger, but data and validation requirements are substantial | Future extension |

### Idea 1 — recommended first paper

**Title:** Freshness-Aware Local–Cloud Telemetry for Reliable Hydroponic Monitoring: Design and Fault-Injection Evaluation of HyGrow

**Question:** Does explicit sample identity and quality-aware local/cloud selection reduce acceptance of stale, invalid or duplicate observations during failures?

**Existing foundation:** Canonical snapshots; hardware/boot/sequence identifiers; local and Firestore transports; health/simulation metadata; freshness and alert-eligibility rules.

**Minimum additions:** An append-only research logger, independent failure timestamps, reproducible replay cases, baseline configurations and a results script. Do not add pumps, another cloud service or a new dashboard for this study.

**Compare:** Cloud-only with the same quality rules; local-only with the same quality rules; combined transport with the rules; and a labelled experimental ablation that accepts the last received numeric values without quality checks. The ablation is a research comparator, not a claim about every competing product.

**Experiments:** Normal operation; WAN disconnected while LAN remains available; LAN loss while cloud remains available where the test arrangement permits it; cloud permission denial; board reboot; stalled sensing with responsive HTTP; simulated/disabled/failed sensor states; duplicate and delayed records through replay. Distinguish physically induced failures from synthetic transport traces.

**Measure:** Fraction of time a genuinely fresh valid reading is available; stale-as-fresh acceptance rate; duplicate-observation rate; recovery time; p50/p95 observation delay; and number of alerts accepted or suppressed by ground-truth event type. Also report missed alerts caused by gating, not just fewer false alerts. Use run/event-level confidence intervals rather than treating adjacent samples as independent repetitions.

**Candidate contribution:** A reproducible failure taxonomy, telemetry contract and end-to-end evaluation of their combined behavior. Do not claim first offline hydroponics system: R2–R4 already cover closely related functionality.

**Proposed abstract**

Reliable hydroponic monitoring requires distinguishing a reachable device from a fresh and valid sensor measurement. A successfully delivered response can contain an old, failed, simulated, or previously observed sample, limiting the value of connectivity alone as an indicator of monitoring reliability. This paper proposes an experimental evaluation of HyGrow, an ESP32-S3 hydroponic monitoring platform with local and cloud telemetry paths. Its telemetry contract associates measurements with hardware identity, boot identity, sample sequence, sample age, and sensor-health metadata. The study will compare cloud-only, local-only, and combined transport configurations, alongside an ablation that omits quality checks. Controlled experiments will introduce internet outages, local-path failures, device reboots, sensor-task stalls, and delayed or duplicated observations. Evaluation will measure fresh-data availability, stale-data acceptance, observation duplication, recovery time, and alert validity using independently recorded fault events. An extended physical deployment will assess monitoring continuity under ordinary operating conditions. The intended contribution is a reproducible framework for evaluating measurement validity across transport failures, rather than relying exclusively on message delivery or device online status. The study will establish the benefits and limitations of applying explicit telemetry provenance and freshness policies to low-cost hydroponic monitoring.

**Keywords:** hydroponics; ESP32-S3; telemetry; data freshness; fault injection; edge–cloud monitoring.

### Idea 2 — recommended second paper

**Title:** Capture-Condition Robustness and Selective Prediction in Lightweight Plant Disease Classification: A HyGrow Case Study

**Question:** Under unfamiliar lighting, backgrounds and image quality, when is the current classifier wrong with high confidence, and does a predefined recapture/abstention policy improve the error–coverage trade-off?

**Existing foundation:** MobileNetV3-Large, 15 classes, leaf-grouped preparation, training/evaluation scripts and ONNX inference.

**Minimum additions:** Retrain on the documented split; save logits; add calibration/unknown-input metrics; collect a paired capture dataset; fit a temperature parameter using a held-out calibration set; evaluate a simple accept/recapture/defer rule. Keep inference on a laptop/server for the study; native phone inference is a separate extension.

**Data:** PlantVillage held-out data for the existing task; label-aligned PlantDoc categories for external disease-image evaluation; original photos from the team's plants for capture-condition testing. Use only actual supported crop–condition labels for known-class results. Unknown crops, unrepresented diseases and non-leaf inputs form separate rejection tests. PlantDoc is not a hydroponic validation dataset.

**Original small dataset:** Aim for 60–100 independent leaves across available plants, photographed under four predefined conditions such as diffuse daylight, grow lighting, clutter and mild blur. Record leaf, plant, session, condition and crop identifiers. Group all views of a leaf together; where feasible separate plants/sessions as well. Hundreds of photos of five leaves remain five leaf-level units. If only healthy leaves are available, report healthy-leaf and capture robustness locally, and use the external benchmark for disease categories. Do not infer disease labels from model predictions.

**Compare:** Uncalibrated forced classification; calibrated forced classification; confidence-only abstention; and a predefined quality-plus-confidence rule with at most one prescribed recapture. Fix the second-capture procedure before test evaluation. Do not pick the correctly classified image retrospectively.

**Measure:** Accuracy and macro-F1 for supported labelled classes; expected calibration error, Brier score and negative log-likelihood; accepted-prediction error versus coverage; unknown-input false acceptance; recapture rate, defer rate, and total inference time. Report risk at matched achieved coverage, not just at the same confidence threshold. Bootstrap by leaf or parent image.

**Candidate contribution:** An original paired-capture benchmark and evidence about a practical recapture policy for this lightweight deployed pipeline. R10 already combines cross-domain evaluation, calibration and selective prediction; those ingredients alone do not make this paper new. Calibration need not improve classification or rejection under domain shift, and a negative result can still be informative if carefully measured.

**Proposed abstract**

Plant disease classifiers trained on controlled leaf-image datasets may produce confident errors when photographs differ in illumination, background, or image quality. This paper proposes a deployment-oriented evaluation of the MobileNetV3-Large classifier used in HyGrow, covering fifteen leaf-condition classes across bell pepper, potato, and tomato. The study will combine a leaf-grouped PlantVillage evaluation, a label-aligned external benchmark, and an original paired-image dataset collected under predefined capture conditions in the team's growing setup. The evaluation will compare forced classification, temperature-scaled predictions, confidence-based abstention, and a quality-aware policy that permits one predefined recapture before deferring uncertain cases. Calibration parameters and decision thresholds will be selected using data separated from final testing by physical leaf or source image. Performance will be assessed using macro-F1, calibration error, Brier score, accepted-prediction error, coverage, unknown-input acceptance, and recapture cost. Locally collected healthy-leaf evidence will be distinguished from externally labelled disease-image results. The intended contribution is a reproducible capture-condition benchmark and an assessment of when a lightweight classifier should answer, request another image, or defer. The study will characterize practical reliability limits without assuming that high controlled-dataset accuracy establishes hydroponic disease-diagnosis performance.

**Keywords:** plant disease classification; MobileNetV3; domain shift; confidence calibration; selective prediction; capture robustness.

### Idea 3 — best replacement if image labels are unavailable

**Title:** Dependency-Aware TDS Monitoring under Water-Temperature Sensor Failure in a Low-Cost Hydroponic Platform

**Minimum change:** Log raw probe voltage, compensation temperature, fallback state and reference readings. Existing firmware already performs measured-temperature compensation and flags fallback; the study evaluates their consequences.

**Method:** On a separate bench reservoir, compare uncompensated readings, fixed 25 °C compensation and measured-temperature compensation across multiple solution concentrations and temperatures within the probe's verified operating range. Induce temperature-probe loss and stale input. Compare accept-all alerts, blanket suppression and a proposed bounded-uncertainty policy. A reference EC/TDS meter must have documented calibration and reporting conventions; do not compare incompatible ppm scales. Standards and a suitable reference instrument are required additional resources.

**Measures:** Bias/MAE/RMSE, agreement around predefined alert boundaries, false/missed alerts, and usable-data coverage. Repeat across independently prepared solutions/days and reserve conditions for testing. Calibration and physical error modelling must use training/calibration observations only.

**Novelty limit:** Compensation and calibration are established (R3, R11). The candidate contribution is evaluation of dependency failure and the trade-off between misleading decisions and unavailable decisions. This is separate from Paper 1 only if it concentrates on measurement error and uncertainty, with its own experiment.

**Proposed abstract**

Low-cost TDS monitoring depends on water-temperature measurements used for compensation. When the temperature probe fails, substituting a fixed value can preserve numerical output while changing measurement error and threshold decisions. This paper proposes an experimental study of dependency-aware TDS monitoring in HyGrow. A bench protocol will vary solution concentration and temperature while comparing uncompensated, fixed-temperature, and measured-temperature readings against a calibrated reference. Temperature-probe loss and stale inputs will be introduced separately from normal operation. The study will evaluate whether explicit dependency flags and bounded uncertainty can improve decision validity compared with unconditional acceptance or blanket suppression. Measurement error, false and missed alerts, and usable-data coverage will be reported. The intended contribution is evidence about degraded sensing behavior and its consequences for hydroponic monitoring decisions.

### Idea 4 — sensor-fault diagnosis

**Title:** Resource-Constrained Detection of Sensor Faults in Hydroponic Telemetry Using Health Metadata and Temporal Features

**Minimum change:** Persist time series; compute rolling variance, rate of change and stuck-value features; compare a small rule set with a compact trained classifier. Run the detector on the ESP32 only if its timing/memory cost is measured.

**Method and novelty limit:** R5 and R6 already detect hydroponic sensor faults. A differentiating study would separate transport staleness, simulated data, disconnections and plausible-valued sensor faults under a reproducible resource budget. Use real unplug/reconnect events plus explicitly labelled synthetic bias, drift and spikes. Split time series chronologically before injecting faults; overlapping windows from a run must remain in one partition. Stable real water conditions are not automatically a stuck fault. Measure event recall, false alarms per hour, detection delay and device resource use.

**Proposed abstract**

Hydroponic telemetry can become unreliable through sensor faults even when measurements remain within plausible numeric ranges. This paper proposes a lightweight fault-detection pipeline combining sensor-health metadata with temporal features derived from pH, TDS and environmental observations. Rule-based checks and compact machine-learning baselines will be evaluated using continuous physical measurements, controlled disconnections, and explicitly labelled synthetic perturbations. Chronological and event-separated partitions will prevent leakage between training and evaluation. Performance will be assessed through fault-event recall, false alarms per hour, detection delay, memory use and execution time. The study will examine whether metadata and simple temporal context offer a useful deployment trade-off on resource-constrained hardware, while reporting separately the performance on real and simulated faults.

### Idea 5 — fewer cloud messages without hiding events

**Title:** Event-Triggered Cloud Telemetry with Explicit Freshness Guarantees for Hydroponic Monitoring

**Minimum change:** Keep sensing fixed; change only the upload decision. Upload on meaningful changes, sensor-quality transitions and a maximum heartbeat deadline. Queue/retry policy must be explicit. Do not call this adaptive sampling if sensing frequency stays fixed.

**Method and novelty limit:** R4 already has adaptive agricultural sensing and synchronization. Compare fixed uploads, change-only uploads and change-plus-quality-plus-heartbeat uploads on the same recorded traces, then validate on hardware. Account for all retries and bytes. Measure threshold-event recall and delay, maximum cloud sample age and message count. Lower message counts do not prove lower power consumption; energy claims require a meter. This may fit as an extension of Paper 1 rather than a separate paper.

**Proposed abstract**

Periodic cloud transmission can repeatedly upload nearly unchanged hydroponic readings, while change-only transmission can leave remote observations stale. This paper proposes an event-triggered upload policy that retains fixed-rate sensing and transmits on measurement changes, sensor-quality transitions or a maximum heartbeat deadline. The policy will be compared with fixed-period and change-only baselines using identical telemetry traces and a physical ESP32 deployment. Evaluation will measure transmitted messages and bytes, cloud sample age, threshold-event recall, detection delay and reconnection behavior. Retry traffic and dropped observations will be included in the analysis. The intended contribution is a reproducible assessment of the communication–freshness trade-off for hydroponic monitoring, without inferring energy savings from message reduction alone.

### Idea 6 — model deployment/compression

**Title:** Accuracy, Calibration and Latency Trade-offs in Compressed ONNX Plant Disease Classification

**Minimum change:** Produce supported compressed model variants; verify preprocessing and numerical equivalence; benchmark the same CPU/server target. Actual mobile inference integration is extra work. The current MobileNetV3-Large artifact should not be assumed suitable for ESP32 deployment.

**Method and novelty limit:** R12 already examines MobileNetV3 INT8 quantization failure. Compare FP32 and supported lower-precision variants under both controlled and external-image conditions, reporting failed conversions as outcomes. Evaluate confidence reliability and per-class changes as well as size/latency. Hold runtime, thread counts, warm-up and hardware fixed; distinguish inference latency from image upload/network time. Consider incorporating this into Paper 2 instead of producing a substantially overlapping third AI paper.

**Proposed abstract**

Compressing plant disease classifiers can reduce storage and computation, but deployment decisions based only on model size may overlook prediction changes. This paper proposes a reproducible benchmark of floating-point and compressed ONNX variants of HyGrow's leaf classifier. Models will be evaluated on the same supported-label test sets under controlled and unfamiliar image conditions, using identical preprocessing and a fixed hardware/runtime configuration. Metrics will include macro-F1, per-class recall, calibration error, prediction agreement, artifact size, memory consumption and warm and cold inference latency. Confidence thresholds will be evaluated again after compression. The study will identify deployment trade-offs and conversion failures without assuming that lower numerical precision preserves either accuracy or confidence reliability.

### Idea 7 — recovering historical records after outages

**Title:** Bounded Store-and-Forward Telemetry for Recoverable Hydroponic Monitoring under Intermittent Connectivity

**Minimum change:** Add a bounded local archive with explicit sample keys, acknowledgement/deletion rules, overflow policy and crash recovery. LittleFS existing on the board does not mean a telemetry queue already exists. Use RAM or a separate storage partition/file design as appropriate; measure flash-write behavior.

**Method and novelty limit:** Store-and-forward is already established, including R4. The candidate contribution would be a reproducible recovery/correctness study of this specific bounded implementation. Test outage duration, reboot during write/upload, duplicate acknowledgements, buffer overflow and backlog drain. Use a separate archival path so old records cannot overwrite the current-state document and appear newly fresh. Measure recovery completeness, duplicate records, chronology, time to drain and storage/write overhead. Often best added to Paper 1 rather than submitted as a separate overlapping paper.

**Proposed abstract**

Local access can preserve live hydroponic monitoring during internet outages without guaranteeing recovery of the missing historical record. This paper proposes a bounded store-and-forward extension to HyGrow that retains sample identity across temporary disconnections and device restarts. The design will define acknowledgement handling, overflow behavior and the separation between current-state telemetry and historical uploads. Experiments will interrupt connectivity and power at different stages of collection and synchronization, including cases that exceed buffer capacity. Evaluation will measure recovered-record completeness, duplicates, ordering, backlog-drain time, storage overhead and write frequency. The intended contribution is a reproducible characterization of recovery limits and consistency behavior under constrained storage, rather than an assumption of lossless operation during arbitrarily long outages.

### Idea 8 — future work with stronger data requirements

**Title:** Quality-Gated Multimodal Triage of Plant Health Using Leaf Images and Hydroponic Environment Histories

**Minimum change:** This is not just joining the image API response with current sensor values. It requires timestamp-linked plant/leaf observations, suitable histories, crop context and independent labels distinguishing disease, nutrient stress and normal plants. Unless such data already exists, budget more than 1–2 weeks.

**Method and novelty limit:** Collect observations across independent plants, sessions and relevant conditions. Compare image-only, sensor-only, naive fusion and quality-gated fusion, using plant-separated and time-separated testing. The current 15 classes do not include a general nutrient-deficiency class. Do not infer causal diagnoses from pH/TDS correlations or create ground truth from the same rules/model being tested. Report missing modalities and diagnostic ambiguity. Controlled experiments or expert assessment are necessary to support stronger claims.

**Proposed abstract**

Leaf symptoms can be visually ambiguous, while hydroponic environment histories provide context that may be missing from image-only assessment. This paper proposes a multimodal plant-health triage framework combining leaf-image features with temporally aligned pH, TDS, temperature and humidity observations. Fusion will be conditioned on measurement freshness, sensor availability and simulation status. Using independently labelled plant observations, the study will compare image-only, sensor-only, naive fusion and quality-gated fusion under plant-separated evaluation. Performance will be assessed through class-specific recall, calibration, deferred-case rate and robustness to missing modalities. The intended contribution is evidence about whether trustworthy environmental context improves triage; the framework will not treat environmental correlations as confirmation of a particular pathogen or nutrient deficiency.

## A feasible two-week plan

| Days | Telemetry paper | Classifier paper |
| --- | --- | --- |
| 1–2 | Freeze baseline revision; define event labels; start append-only logging and normal-operation soak | Verify labels/dataset revision; prepare leaf-grouped data; start retraining; define capture protocol |
| 3–4 | Configure baselines and replay harness; pilot timing and failure records | Capture paired plant photos; curate external class mapping and unknown inputs |
| 5–7 | Repeated outages, reboot, sensor-stall and state tests; keep independent event logs | Fit calibration/thresholds on calibration data; implement one-recapture evaluation |
| 8–10 | Complete soak and controlled trials; repeat questionable cases | Lock final test data and run comparisons; document local versus external evidence |
| 11–12 | Calculate metrics and run-level intervals; prepare failure timelines and tables | Calculate metrics and leaf-level intervals; prepare risk–coverage and condition plots |
| 13–14 | Write methods, results and limitations; check claims against evidence | Write methods, results and limitations; audit overlap with R10 and with Paper 1 |

Suggested team allocation: one person owns firmware and physical tests, one owns app/replay logging, one owns model/evaluation, and one owns image curation, references and manuscript coordination. Each paper still needs all authors to review its evidence.

Suggested minimum pilot target for telemetry: 10 independently initiated trials per selected failure scenario, several outage durations, and a 3–7 day normal-operation record. These are planning targets, not proof of statistical adequacy. Adjust after observing variability. Report exact hardware count; one board supports a single-device case study, not a multi-farm scalability claim.

For timing, use independently recorded event/sample times where possible. The app's sample-age calculation estimates sample time from response/upload timing; transport delay and clock skew can introduce error. State clock synchronization and timestamp uncertainty instead of presenting estimated sample age as exact end-to-end Age of Information. An HTTP identity field validates consistency with a configured board; it is not cryptographic proof of authenticity.

## Keep the two papers substantively separate

| Aspect | Paper 1 | Paper 2 |
| --- | --- | --- |
| Main question | Validity and continuity of environmental observations during failures | Reliability of image predictions under capture changes |
| Main evidence | Device traces, fault events and transport observations | Labelled images, capture metadata and logits |
| Main metrics | Fresh-data availability, stale acceptance, recovery and alert validity | Macro-F1, calibration, error–coverage trade-off and recapture cost |
| Main new artifact | Failure protocol and telemetry evaluation traces | Paired capture-condition dataset and evaluation protocol |
| Shared context | HyGrow platform description | HyGrow platform description |

Describe the shared platform briefly and cite the companion work where appropriate. Avoid reusing the same experimental contribution, figures or abstract under a second title. Ideas 4, 5 and 7 overlap with Paper 1; Idea 6 overlaps with Paper 2. Combine related contributions unless each study has a substantial independent question and dataset.

## Claims the present evidence does not support

- “First IoT/AI hydroponics system,” “first offline hydroponic system,” or “novel MobileNet architecture.”
- New leaf-grouped splitting or temperature scaling; both are established methods.
- Improved yield, biomass, water efficiency or fertilizer efficiency from this monitoring implementation without controlled agronomic comparisons.
- Continuous historical recovery, sensor self-healing or autonomous pump control in the current code.
- General disease diagnosis on hydroponic crops based solely on PlantVillage performance.
- Detecting disease before visible symptoms using the present still-image classifier.
- Nutrient-deficiency diagnosis using labels the model was never trained to recognize.
- Laboratory-confirmed diseases from a public-image label or the model's own prediction.
- Guaranteed real-time, secure or scalable operation without appropriate timing, security or scale experiments.

The practical objective is two narrow, well-supported papers, with modest claims and original evidence that another team can reproduce.
