# Kimi project plan

## Goal

Build a Windows desktop voice-command assistant that recognizes a small, fixed vocabulary on the local machine and performs simple, safe actions. Training may use an NVIDIA GPU; everyday inference should run offline, including without a GPU if practical. The reference proposal estimates 3-5 weeks part-time. This is a planning estimate, not a delivery promise.

## Definition of done

- A user can install and run Kimi on the target Windows machine, disconnect the network, start and stop listening, and see the predicted command and confidence.
- The assistant recognizes 5-10 fixed commands, rejects unknown speech and background audio, and performs two or three safe local actions. No audio leaves the machine during operation.
- The training and evaluation pipeline is reproducible, with speaker-disjoint train, validation, and test sets where speaker metadata is available.
- An evaluation report includes per-command precision and recall, a confusion matrix, false activations on background audio, and results for unseen speakers and noisy conditions.
- The repository provides application and training source, either a distributable trained model or reproducible instructions to obtain and train it, setup instructions, evaluation results, and a short demo video or a link to it.

## Initial scope and decisions to validate

- **Platform:** Windows desktop, Python, PyTorch and torchaudio for training and audio processing, and PySide6 for the interface. SQLite is optional and should be added only if persistent settings or history are useful.
- **First action:** a local countdown timer. Proposed commands are `start`, `stop`, `zero` (reset), `up` (add time), and `down` (subtract time). These are candidate Speech Commands labels; confirm their availability in the chosen dataset version. Define exact timer increments and behavior before implementation.
- **Recognition:** classify short audio windows into the selected commands, unknown speech, and background/silence. Use a small CNN on log-mel spectrograms as the baseline. Confidence is a model score, not automatically a calibrated probability.
- **Safety:** no arbitrary shell commands, file deletion, purchases, or online services. Require a user-controlled listening switch. Reject uncertain predictions and use a cooldown or equivalent to prevent repeated actions from one utterance. Keep raw microphone recordings and personal data out of version control.
- **Offline boundary:** downloading dependencies and data is allowed during setup; inference and its required model files must work without network access once installed. Verify this with the network disconnected. Check dataset, dependency, and model licenses before distributing artifacts.
- **Open questions:** target NVIDIA GPU and VRAM; desired microphone and language/accent coverage; exact command vocabulary and timer behavior; whether the model and demo can be redistributed. Record decisions and actual measurements as the project progresses.

## Milestones

### 1. Establish the baseline and environment

- [ ] Record Windows version, Python version, GPU model, VRAM, driver and CUDA/PyTorch compatibility; verify both GPU training and a CPU inference path.
- [ ] Set up a reproducible Python environment and a small project structure for data, training, inference, desktop UI, and tests. Document installation and commands.
- [ ] Select the dataset version and verify its license, available command labels, download method, and speaker identifiers. Keep downloaded data outside Git.
- [ ] Agree on the command-to-action table and initial timer settings.

**Exit criterion:** a fresh environment can be set up and an audio sample can be loaded and converted into a log-mel spectrogram. Hardware and unresolved constraints are recorded.

### 2. Prepare data and train a baseline

- [ ] Create reproducible train, validation, and test manifests with fixed seeds. Use the dataset's prescribed splits when suitable and verify speaker IDs do not overlap. Keep any custom speakers in only one split.
- [ ] Map the selected words to command classes, reserve other words for unknown examples, and include background/silence examples. Avoid overlapping source recordings or augmented variants across splits.
- [ ] Implement consistent resampling, padding/windowing, and log-mel preprocessing for both training and inference. Apply noise augmentation only to training data; keep clean and noisy evaluation sets controlled and separate.
- [ ] Train a small CNN, record configuration, data version, seed, hardware, duration, and metrics, and save the best checkpoint based on validation performance.

**Exit criterion:** another run can reproduce the pipeline and produce a baseline model and validation metrics; preprocessing and label mapping are saved with the model.

### 3. Evaluate and choose operating thresholds

- [ ] Evaluate once on held-out speakers with per-class precision/recall, confusion matrix, and clean versus noisy performance. Report sample counts and conditions.
- [ ] Measure false activations per hour of representative background audio, including silence, household noise, and non-command speech. Keep the test material separate from training and threshold selection.
- [ ] Select rejection thresholds and cooldown on validation data, then report test results without retuning on the test set. Measure command latency and CPU resource use on the target machine.
- [ ] Set practical quality targets after the first baseline and intended environment are known; document failures rather than treating unmeasured targets as achieved. If useful, compare noise augmentation with an otherwise comparable unaugmented model.

**Exit criterion:** an evaluation report states the dataset split, operating threshold, test conditions, measured metrics, limitations, and a decision on whether the model is ready to integrate.

### 4. Build the offline desktop assistant

- [ ] Implement microphone capture and inference using the same preprocessing as training. Handle missing devices, permission denial, model loading errors, and audio-device changes clearly.
- [ ] Add a PySide6 interface with start/stop listening, microphone status, predicted command, confidence display, rejection feedback, and timer controls and state.
- [ ] Route accepted commands through an explicit allowlist to timer actions. Ensure stop-listening releases the microphone, reject low-confidence or unknown speech, and suppress duplicate triggers.
- [ ] Keep inference, UI, and action handling separate so they can be tested without a microphone or GUI. Do not retain raw audio by default.

**Exit criterion:** the five chosen commands control the timer locally, unknown/background audio does not intentionally trigger an action, and listening can be stopped visibly and reliably.

### 5. Verify, package, and demonstrate

- [ ] Test preprocessing and label consistency, command routing, threshold and cooldown behavior, timer transitions, and microphone error handling. Exercise the UI and real microphone manually.
- [ ] Test a clean install and an offline session on the target machine, including model availability, start/stop listening, unknown phrases, noisy conditions, and CPU fallback if supported.
- [ ] Document setup, training, evaluation, model provenance and license, known limitations, and troubleshooting in the user-facing README and report.
- [ ] Record a short demo: start and stop a timer, show confidence and rejection of an unknown phrase or background noise, and present clean/noisy results.

**Exit criterion:** the deliverables in the definition of done exist and a new user can follow the documented steps to reproduce the demo.

## Suggested sequence

| Part-time period | Focus | Evidence of progress |
| --- | --- | --- |
| Week 1 | Environment, vocabulary, dataset and splits | Reproducible audio preprocessing and documented hardware |
| Week 2 | Baseline CNN and training | Checkpoint and validation results |
| Week 3 | Evaluation and thresholds | Held-out report, noise and false-activation measurements |
| Week 4 | Desktop UI and timer integration | Working offline command-to-action loop |
| Week 5, if needed | Hardening, packaging and demo | Reproducible installation, report and demo |

## Project log

- 2026-11-04: Initial repository created and initial commit published. Project is in planning; no model or application has been built yet.
