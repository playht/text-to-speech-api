# Play.ht Text-to-Speech API
![](https://s3.amazonaws.com/static.play.ht/text-to-speech-api.png)

Access all the best text-to-speech AI voices from Google, Amazon, IBM and Microsoft using Play.ht's text-to-speech API. Our [AI voice generator](https://play.ht) provides a single interface to convert text to audio using voices across different providers. 

Using a single text-to-speech API in your projects saves you time and offers many benefits:
1. You instantly get access to all the voices from Google, Amazon, IBM and Microsoft.
2. You maintain only one API integration.
3. You don't have to worry about API upgrades or changes made on Google, Amazon, IBM and Microsoft.
4. Any new voices added on these platforms are instantly available to you.

Take a look at the [Voices reference file](Voices.md) to see a list of the available voices and languages. The file also contains audio samples to help you pick.

**Note:** You need to have a Play.ht account with word credit to be able to access the API.

## Overview of API

There are two endpoints on the API that you will use to convert text to speech:
1. `/convert`: Performs the text-to-speech conversion.
2. `/articleStatus`: Lets you know if the conversion is done.

Since the text-to-speech conversion is an asynchronous process, you will first make a `POST` request to the `/convert` endpoint with the text and voice, and then make `GET` requests to the `/articleStatus` endpoint to check if the conversion is done and to get the audio file.

The two endpoints have been described in detail below.

But first, we need authentication!

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

  `voice` is the ID of the voice used to synthesize the text. Refer to the [Voices reference file](Voices.md) for more details.

  Only one of `content` or `ssml` can be passed:

    - `content` is an array of strings, where each string represents a paragraph in plain text format.

    - `ssml` is an array of strings, where each string represents a paragraph in SSML format. [Learn more about SSML](https://www.w3.org/TR/speech-synthesis/). Not all SSML features are supported with all voices.

  `title` is a field to name your file. You can use this name to find the audio in your Play.ht dashboard.
  
  `narrationStyle` is a string representing the tone and accent of the voice to read the text. Make sure the value for `narrationStyle` is supported by the voice in your request.

  `globalSpeed` is a string in the format `<number>%`, where `<number>` is in the closed interval of `[20, 200]`. Use this to speed-up, or slow-down the speaking rate of the speech.

  `pronunciations` is an array of key-value pair objects, where `key` is the source string (e.g. `"Play.ht"`), and `value` is the target pronunciation (e.g. `"Play dot H T"`). Use this when you want to customize the pronunciation of a certain word/phrase (e.g. your brand name).

- Response (JSON):
  ```jsonc
  {
    "status": "transcriping" | "error",
    "transcriptionId": string,
    "error": string // Optional
  }
  ```

  Use the `transcriptionId` in the response to check the conversion status in the [Article status](#article-status) endpoint.

### Transcription status

- Endpoint:  `./articleStatus?transcriptionId={transcriptionId}`

Use this endpoint to check the conversion status of your text using its transcription ID.

If the article (text) is converted to audio, the response will contain the audio file URL along with certain metadata such as voice and narration style. A `true` value for `error` field indicates a conversion failure.

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
