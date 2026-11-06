# Kimi setup guide

This guide prepares your Windows computer for the Kimi offline voice-command assistant. Kimi is currently a planned project; the application and trained model have not yet been built.

## What is already available

On the computer checked on 4 November 2026:

- Python 3.11.7 and Git are installed.
- Visual Studio Code is available.
- A project-specific Python environment has been created at `.venv` in the project folder.
- Intel Iris Xe graphics were detected. No NVIDIA GPU was available to the existing PyTorch installation, so the initial setup below uses the CPU.

## What you need

1. **Python:** Already installed. Python is the only programming language needed for the initial project.
2. **Git:** Already installed. It tracks project changes and connects the project to GitHub.
3. **Visual Studio Code:** Use the existing installation to edit and run code.
4. **PyTorch and torchaudio:** Install these Python packages for model training and audio processing.
5. **PySide6:** Install this Python package to build the desktop interface.
6. **A microphone:** Use your built-in microphone or a USB headset.
7. **Disk space:** Allow several gigabytes for the training dataset and its extracted files. One published Speech Commands dataset configuration lists a 2.37 GiB download and 8.17 GiB extracted size; actual requirements depend on the version and pipeline used.

## Install the Python packages

Connect to the internet, open PowerShell in the project folder, and run:

```powershell
.\.venv\Scripts\python.exe -m pip install torch torchaudio --index-url https://download.pytorch.org/whl/cpu

.\.venv\Scripts\python.exe -m pip install PySide6
```

The commands install packages only into the Kimi environment. They may take some time and require internet access. The intended assistant should run offline after its application, dependencies, and model have been installed.

PyTorch offers a [Windows installation selector](https://pytorch.org/get-started/locally/) for checking the current CPU installation command. PySide6 includes the Qt binaries required for this setup, so a separate Qt download is not necessary. See the [Qt for Python getting-started guide](https://doc.qt.io/qtforpython-6/gettingstarted.html).

The Speech Commands dataset will be downloaded when the training pipeline is built; you do not need to download it manually now. See the [dataset catalog](https://github.com/tensorflow/datasets/blob/master/docs/catalog/speech_commands.md) for an example of its size and contents.

## Set up the target hardware

The **target hardware** is the computer and microphone on which Kimi should work. The initial choice is this Windows computer, its CPU, and your usual microphone.

1. Connect the microphone you plan to use.
2. Open **Windows Settings > System > Sound > Input**. Select the microphone, speak, and check that the input meter moves.
3. Test in a typical environment, such as your desk with its usual background noise.
4. Keep enough free disk space for the dataset, packages, and trained model.
5. If you have another computer with an NVIDIA GPU and want to train there, note its GPU model and VRAM. The model can still be designed to run on this computer's CPU.

You do not need an NVIDIA driver or the CUDA Toolkit for the CPU setup above. Training on a CPU may take longer than training on a suitable GPU.

## Decide how the commands should work

**Command behavior** means deciding which words Kimi listens for and what happens when each word is recognized. The following is a proposed first version, not an implemented feature:

| Spoken word | Proposed timer action |
| --- | --- |
| Start | Start or resume the countdown. |
| Stop | Pause the countdown. |
| Zero | Reset the timer. |
| Up | Add one minute. |
| Down | Subtract one minute, stopping at zero. |

The suggested initial timer is five minutes. The app should show a **Start listening / Stop listening** control and ignore uncertain or unknown speech. Confirm or change these choices before implementation.

The name **Kimi** does not automatically make it a wake word. The first version can listen while its button is enabled. A wake word can be considered later and would require additional data and testing.

## Next decisions

- Confirm whether this computer and microphone are the target setup.
- Confirm or change the five timer commands, their actions, and the initial duration.
- If using a different training computer, record its GPU model and VRAM.

The longer implementation milestones and completion criteria are in `PROJECT_PLAN.md`.
