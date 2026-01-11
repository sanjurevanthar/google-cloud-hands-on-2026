# Speech-to-Text Transcription with Cloud Speech API

## Overview
This lab illustrates how to perform synchronous speech transcription using Google Cloud's Speech-to-Text API. A pre-recorded FLAC audio file stored in Cloud Storage is submitted to the recognition endpoint via REST. The API returns a transcript string and a confidence value. The lab also demonstrates multilingual recognition by modifying the language configuration and submitting a second request.

## Learning Objectives
By completing this exercise, we become familiar with:
- Creating API authentication using an API key
- Structuring a Speech-to-Text recognition request in JSON format
- Executing synchronous recognition through `curl`
- Extracting confidence and transcript values from the API response
- Modifying recognition parameters for multilingual transcription

## Services and Components
The following Google Cloud services and components are utilized:
- Speech-to-Text API
- Cloud Storage (sample audio assets)
- Compute Engine (sandbox VM for execution)
- curl (HTTP invocation client)

## Execution Flow Summary
1. Obtain an API key for Speech-to-Text access
2. Configure an environment variable on the compute instance
3. Build a JSON payload targeting a sample English audio file on Cloud Storage
4. Submit the request to the Speech-to-Text endpoint using curl
5. Review the returned transcription and confidence score
6. Modify the request payload to specify French transcription and repeat execution

## Input Audio Samples
The exercise references the following sample assets:
- English FLAC file: `gs://cloud-samples-data/speech/brooklyn_bridge.flac`
- French FLAC file: `gs://cloud-samples-data/speech/corbeau_renard.flac`

## Output Behavior
The Speech-to-Text API responds with:
- A recognized transcript
- A numeric confidence score
- A language code associated with recognition

These fields provide an interpreted transcription of the spoken audio content.

## Synchronous vs Asynchronous Use Cases
Synchronous recognition is appropriate for short-duration recordings since the client waits for completion. For longer recordings, asynchronous recognition is recommended to prevent blocking. Streaming recognition is available for real-time transcription scenarios.

## Repository Artifacts
- `commands.md` contains the exact commands executed
- `request.en.json` and `request.fr.json` contain valid recognition payloads
- `notes.md` captures observations and troubleshooting data

