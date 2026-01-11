# Commands for GSP048 Speech-to-Text Lab

# Export Speech-to-Text API key (replace placeholder with actual key value)
export API_KEY="<YOUR_API_KEY>"

# Submit English synchronous recognition request
curl -s -X POST \
  -H "Content-Type: application/json" \
  --data-binary @request.en.json \
  "https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" \
  > result.en.json

# Inspect English transcription output
cat result.en.json

# Submit French synchronous recognition request
curl -s -X POST \
  -H "Content-Type: application/json" \
  --data-binary @request.fr.json \
  "https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" \
  > result.fr.json

# Inspect French transcription output
cat result.fr.json
