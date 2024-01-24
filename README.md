
# Transcription Stream
Created by [https://transcription.stream](https://transcription.stream) with special thanks to [MahmoudAshraf97](https://github.com/MahmoudAshraf97) and his work on [whisper-diarization](https://github.com/MahmoudAshraf97/whisper-diarization/), and to [jmorganca](https://github.com/jmorganca/ollama) for Ollama and its amazing simplicity in use.

## Overview
Create a turnkey self-hosted offline transcription diarization service with Transcription Stream.

## FORK
SSH and web interface removed in this fork, diarize hardcoded and whisper model set to medium.
By setting the example.env to .env, the local path of the volume is defined.


**Prerequisite: NVIDIA GPU**
> **Warning:** The resulting ts-gpu image is 23.7GB and might take a hot second to create

## Build and Run Instructions
### Automated Setup and Run
```bash
chmod +x install.sh;
./install.sh;
```

### Manual Setup
### Creating Volume
- **Transcription Stream Volume:**
  ```bash
  docker volume create --name=transcriptionstream
  ```

### Build Images from their respective folders
- **ts-gpu Image:** (23.7GB - includes necessary models and files to run offline)
  ```bash
  docker build -t ts-gpu:latest .
  ```

### Run the Service
- Start the service using `docker-compose`. This provides updates from running jobs:
  ```bash
  docker-compose -p transcriptionstream up
  ```

## Additional Information


### Customization and Troubleshooting
- Change the password for `transcriptionstream` in the `ts-gpu` Dockerfile.
- Uncomment ts-gpt section in `docker-compose.yml` to enable built-in Ollama mistral. Update `install.sh` and `run.sh` for mistral model install and updates.
- Update the Ollama api endpoint url in /ts-gpu/transcribe_example_d.sh if not running ts-gpt
- The transcription option uses `whisperx`, but was designed for `whisper`. Note that the raw text output for transcriptions might not display correctly in the console.
- Both the `large-v3` and `large-v2` models are included in the initial build.
- Update the Ollama api url in ts-gpu/transcribe_example_d.sh prior to install/build
- Change the prompt text in ts-gpu/ts-summarize.py to fit your needs. Update ts-web/templates/transcription.html if you want to call it something other than summary.
- 12GB of vram is not enough to run both whisper-diarization and ollama mistral. Whisper-diarization is fairly light on gpu memory out of the box, but Ollama's runner holds over 10GB of gpu memory open after generating for quite sometime, causing the next diarization/transcription to run our of CUDA memory. Since I can't run both on the same host, I've set the batch size for both whisper-diarization and whisperx to 16, from their default 8.
- I need to fix an issue with ts-web that throws an error to console when loading a transcription when a summary.txt file does not also exist.