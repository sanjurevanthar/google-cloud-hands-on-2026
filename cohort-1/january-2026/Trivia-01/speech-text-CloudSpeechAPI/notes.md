# Notes and Observations for Speech-to-Text Lab

## Language Configuration
Multiple languages can be recognized by adjusting the `languageCode` field in the request payload. Locale variants (e.g., `fr-FR`, `en-GB`) are also supported depending on use case.

## Audio Format Considerations
The samples used in this lab are FLAC-formatted. If alternate input formats are used, the `encoding` parameter must reflect the format. The Speech-to-Text API supports a wide range of encodings including LINEAR16, AMR, and OGG_OPUS.

## Response Interpretation
The synchronous recognition result provides:
- `transcript`: interpreted text resulting from recognition
- `confidence`: model certainty (float, 0 to 1)

Production applications may require multiple alternatives or ranking selection strategies.

## Usage Recommendations
Synchronous transcription works best for short clips. For long files, asynchronous recognition avoids blocking the client.

## Troubleshooting
Common issues include:
- Invalid API keys
- Unsupported language codes
- URI access failures for Cloud Storage objects
