# Text Translation & Language Detection with Cloud Translation API 

## Learning Outcomes
After completing this lab, the following concepts should be understood:
- Creating Translation API requests using REST and API keys
- Translating text between languages
- Performing language detection on arbitrary text
- Understanding response structure (translated text, source language, confidence values)

## Services Used
The lab relies on:
- Cloud Translation API (text translation + detection)
- Cloud Shell or compute instance as execution client
- curl for issuing HTTP requests

## Execution Summary
1. Generate an API key for the Translation API
2. Export the key into an environment variable for convenience
3. Submit a translation request (English to Spanish example)
4. Submit a language detection request for two text samples
5. Inspect returned JSON structures

## Translation Request Example
A basic translation request contains:
- `target`: destination language code (ISO-639-1)
- `q`: input text to translate

The API responds with translated text and detected source language.

## Language Detection Example
Language detection uses the `/detect` endpoint. Multiple inputs can be submitted in a single request to obtain language codes and confidence values.

## Relevant Output Fields
Response JSON typically includes:
- `translatedText`: translated string
- `detectedSourceLanguage`: language code for the input
- `language`: identified language for detection requests
- `confidence`: model confidence score

## Repository Artifacts
- `commands.md`: contains REST calls used in the lab
- `payload/`: contains shell snippets for translation and detection
- `notes.md`: personal observations and troubleshooting notes

## Note
The Translation API supports more than one hundred language pairs and offers premium neural translation for improved quality. Language codes use the ISO-639-1 standard.
