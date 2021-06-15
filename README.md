API
---

## Authentication

All endpoints require authentication. Authentication consists of two required HTTPS headers:
- `Authorization`: This is where your secret key goes.
- `X-User-ID`: This is where your Play.ht user ID goes.

These credentials are sent to you once you register for API access. If you haven't done that yet, you can contact us on `support [at] play.ht`.

Make sure to store your secret key privately and do not share it. Never use your secret key in the front-end part of your app or in the browser.

## Endpoints

- Base URL: `https://play.ht/api/`

### Article status

- Endpoint:  `./articleStatus?transcriptionId={transcriptionId}`

Use this endpoint to check the conversion status of a certain article using its transcription ID.

If the article is converted, the response will also contain the article configuration, e.g. voice and narration style. A `true` value for `error` property indicates a conversion failure.

Where `{transcriptionId}` is the ID provided in the successful response of [Convert](#convert) endpoint.

- Method: `GET`
- Response (JSON):
  ```json
  {
    "converted": boolean,
    ["voice": string,]
    ["narrationStyle": string,]
    ["globalSpeed": string,]
    ["audioDuration": number,]
    ["audioUrl": string,]
    ["error": boolean,]
    ["errorMessage": string,]
  }
  ```

Optional parameters are marked with square brackets, and are only provided when applicable.
