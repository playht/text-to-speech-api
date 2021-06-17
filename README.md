Play.ht's Public API Documentaion
---

## Authentication

All endpoints require authentication. Authentication consists of two required HTTPS headers:
- `Authorization`: This is where your secret key goes.
- `X-User-ID`: This is where your Play.ht user ID goes.

These credentials are sent to you once you register for API access. If you haven't done that yet, you can contact us on `support [at] play.ht`.

Make sure to store your secret key privately and do not share it. Never use your secret key in the front-end part of your app or in the browser.

## Endpoints

- Base URL: `https://play.ht/api/v1/`

### Convert

- Endpoint:  `./convert`

Use this endpoint to start converting an article from text to audio.

- Method: `POST`

- Body (JSON):
  ```jsonc
  {
    "voice": string,
    "content": string[],
    "ssml": string[],
    "title": string,          // Optional
    "narrationStyle": string, // Optional         
    "globalSpeed": string,    // Optional      
    "pronunciations": { key: string, value: string }[], // Optional
  }
  ```

  `voice` is the ID of the voice used to transcribe the text. Refer to the [Voices](https://play.ht/app/voices) page for more details.

  Only one of `content` or `ssml` can be passed:

    - `content` is an array of strings, where each string represents a paragraph in plain text format.

    - `ssml` is an array of strings, where each string represents a paragraph in SSML format. [Learn more about SSML](https://www.w3.org/TR/speech-synthesis/). Not all SSML features are supported with all voices.

  `title` is a field to name your file. The name will be displayed in your [Audio files page](https://play.ht/app/audio-files) in your dashboard.
  
  `narrationStyle` is a string representing the tone of the voice pronouncing the text. Make sure the value for `narrationStyle` is supported by the voice in your request. Refer to the [Voices](https://play.ht/app/voices) page for more details on support.

  `globalSpeed` is a string in the format `<number>%`, where `<number>` is in the closed interval of `[20, 200]`. Use this to speed-up, or slow-down your output audio.

  `pronunciations` is an array of key-value pair objects, where `key` is the source string (e.g. `"Play.ht"`), and `value` is the target pronunciation (e.g. `"Play dot H T"`). Use this when you want to customize the pronunciation of a certain word/phrase (e.g. your brand).

- Response (JSON):
  ```jsonc
  {
    "status": "transcriping" | "error",
    "transcriptionId": string,
    "error": string // Optional
  }
  ```

  Use the `transcriptionId` in the response to refer to the conversion in the [Article status](#article-status) endpoint.

### Article status

- Endpoint:  `./articleStatus?transcriptionId={transcriptionId}`

Use this endpoint to check the conversion status of a certain article using its transcription ID.

If the article is converted, the response will also contain the article configuration, e.g. voice and narration style. A `true` value for `error` field indicates a conversion failure.

Where `{transcriptionId}` is the ID provided in the successful response of [Convert](#convert) endpoint.

- Method: `GET`

- Response (JSON):
  ```jsonc
  {
    "converted": boolean,
    "error": boolean,         // Optional
    "errorMessage": string,   // Optional
    "audioUrl": string,       // Optional
    "audioDuration": number,  // Optional
    "voice": string,          // Optional
    "narrationStyle": string, // Optional
    "globalSpeed": string,    // Optional
  }
  ```

Optional fields are only provided when applicable.
