---
layout: post
title:  "whisper.cpp for macOS"
date:   2024-11-25 21:31:55 +0100
categories: [Programming, Machine Learning]
---

A guide for building whisper.cpp with Metal and Core ML support on macOS.

## Install the Required Build Tools

This command uses Homebrew (macOS's package manager) to install two essential build tools. `cmake` is a cross-platform build system generator that manages the compilation process, while `ninja` is a small, fast build system designed to run builds quickly. These tools are often required dependencies when compiling software from source code.

```bash
brew install cmake ninja
```

## Checkout the whisper.cpp Sources

These commands set up the C++ implementation of the Whisper model. First, `git clone` downloads the whisper.cpp repository to your specified directory (replace `<WHISPER_DIRECTORY>` with your desired path). Then it changes into that directory using `cd`. Finally, it creates and switches to a new branch named "v1.7.2" based on the v1.7.2 tag, ensuring you're using a stable, tested version of the code.

```bash
git clone https://github.com/ggerganov/whisper.cpp <WHISPER_DIRECTORY>
cd <WHISPER_DIRECTORY>
git checkout -b v1.7.2 v1.7.2
```

_Note:_ `v1.7.2` is the newest version right now. You can find the newest version by running `git describe -–tags` anytime.

## Download the whisper.cpp Base Model

This command downloads the optimized GGML version of Whisper's base model. The script `download-ggml-model.sh` is located in the `models` subdirectory and downloads the "base" size model, which is a good balance between accuracy and resource usage. The GGML format is specifically optimized for CPU inference, making it efficient for running on desktop computers.

```bash
models/download-ggml-model.sh base
```

## Configure Hardware Support

### Metal

This command sets up the build configuration for the project using CMake. It uses the Ninja build system generator, creates the build files in a directory named "build", and configures for a release build with optimizations enabled rather than debug settings. This prepares the project for compilation with settings that will give optimal performance.

```bash
cmake -G Ninja -B build -DCMAKE_BUILD_TYPE=Release
```

### CoreML

This command sets up the build configuration for the project using CMake. It's similar to the previous configuration but adds Core ML support through the `-DWHISPER_COREML=1` flag. This enables the project to use Apple's Neural Engine for faster processing on Apple Silicon Macs. The command uses the Ninja build system generator, creates files in a "build" directory, and configures for an optimized release build rather than a debug build.

```bash
cmake -G Ninja -B build -DCMAKE_BUILD_TYPE=Release -DWHISPER_COREML=1
```

## Build

This command compiles the project using the configuration we set up earlier. It tells CMake to build the project using the files in the "build" directory with Release configuration settings, which enables optimizations for best performance. The build process will create the executable files needed to run the program.

```bash
cmake -–build build -–config Release
```

## Test

This command runs the compiled whisper.cpp program to transcribe speech from an audio file. It uses the base GGML model located at `models/ggml-base.bin` to process the sample audio file `samples/jfk.wav` (which contains a JFK speech). The `-m` flag specifies the model path, and `-f` specifies the input audio file path.

```bash
build/bin/main -m models/ggml-base.bin -f samples/jfk.wav
```

