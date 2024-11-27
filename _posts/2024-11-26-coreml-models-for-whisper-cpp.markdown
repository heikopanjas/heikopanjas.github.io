---
layout: post
title:  "CoreML Models for whisper.cpp"
date:   2024-11-26 21:31:55 +0100
categories: development ai coreml whisper
---

A guide for setting up and converting Whisper models to Core ML format on Apple Silicon Macs.

## Miniconda Installation Instructions

These commands install Miniconda on a Mac with Apple Silicon (ARM64) processor. First, it downloads the installation script using curl. Then it runs the script in batch mode (-b), updating any existing installation (-u), and installing to the specified directory (-p). Finally, it removes the downloaded installer file.
Replace `<MINICONDA_DIRECTORY>` with your desired installation path, like `~/miniconda3`.

```bash
curl -LO https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh
bash Miniconda3-latest-MacOSX-arm64.sh -b -u -p <MINICONDA_DIRECTORY>
rm Miniconda3-latest-MacOSX-arm64.sh
```

## Create the Python Environment

These commands set up and activate a new conda environment. First, the `source` command activates `conda` in your current shell session, making the conda command available. Replace `<MINICONDA_DIRECTORY>` with your Miniconda installation path.
The `conda create` command makes a new environment named "py310-whisper" with Python 3.10 installed. The `-n` flag sets the environment name, while `python=3.10` specifies the Python version. The `-y` flag automatically answers "yes" to prompts.
Finally, `conda activate` switches into the newly created environment, where you can start installing and using packages.

```bash
source <MINICONDA_DIRECTORY>/bin/activate
conda create -n py310-whisper python=3.10 -y
conda activate py310-whisper
```

## Install the Required Python Packages

These commands install specific Python packages and their dependencies using pip. The `ane_transformers` package provides support for Apple Neural Engine optimizations. `openai-whisper` is OpenAI's speech recognition model. `coremltools` allows converting models to Apple's Core ML format for optimized performance on Apple devices.
The numpy installation specifies a version constraint to ensure compatibility by requiring any version below 2.0. Similarly, `torch` is pinned to the specific version 2.1.0, which is important for maintaining compatibility between these AI/ML tools.
These version constraints help avoid potential conflicts between packages and ensure the setup works reliably on Apple Silicon Macs.

```bash
pip install ane_transformers
pip install openai-whisper
pip install coremltools
pip install "numpy<2"
pip install torch==2.1.0  (see Note)
```

_Note:_ __Ignore the error messages regarding unsupported torch versions.__

## Download the whisper.cpp Models

These commands set up the GGML version of the Whisper model. First, it changes to the models directory within your Whisper installation (replace `<WHISPER_DIRECTORY>` with your actual Whisper directory path). Then it runs a script to download the "base" size GGML model, which is an optimized version of Whisper designed for CPU inference. The base model offers a good balance between accuracy and resource usage.

```bash
cd <WHISPER_DIRECTORY>/models
download-ggml-model.sh base
```

_Note:_ Other available models are _tiny_, _small_, _medium_, _large-v1_, _large-v2_, _large-v3_ and _large-v3-turbo_.

## Convert the whisper.cpp Models

This command converts the base Whisper model to Core ML format. It runs a script that transforms the previously downloaded GGML base model into Apple's Core ML format, which is optimized for running on Apple Silicon chips. This conversion enables the model to take advantage of the Neural Engine and other hardware accelerators on Mac devices, potentially improving performance and energy efficiency.

```bash
generate-coreml-model.sh base
```

_Note:_ Repeat for other models if needed, e.g. _tiny_, _small_, _medium_, _large-v1_, _large-v2_, _large-v3_ or _large-v3-turbo_.

## Remove the Python Environment

These commands clean up the conda environment setup. First, `conda deactivate` exits the current environment and returns to the base conda environment. Then `conda env remove` deletes the "py310-whisper" environment and all its installed packages, freeing up disk space. This is useful when you're done with your work and want to clean up your system.

```bash
conda deactivate
conda env remove -n py310-whisper
```
