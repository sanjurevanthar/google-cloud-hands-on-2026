# Commands Used

## Export API key
export API_KEY= <YOUR_API_KEY>

## Translate English → Spanish

TEXT="My%20name%20is%20Steve"

curl "https://translation.googleapis.com/language/translate/v2?target=es&key=${API_KEY}&q=${TEXT}"

## Detect Language for Multiple Strings

TEXT_ONE="Meu%20nome%20é%20Steven"

TEXT_TWO="日本のグーグルのオフィスは、東京の六本木ヒルズにあります"

curl -X POST \
  "https://translation.googleapis.com/language/translate/v2/detect?key=${API_KEY}" \
  -d "q=${TEXT_ONE}" \
  -d "q=${TEXT_TWO}"
