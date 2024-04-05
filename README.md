# Play.ht Text-to-Speech API

Access the best text-to-speech AI voices using Play.ht's text-to-speech API. Our [AI voice generator](https://play.ht) provides a single interface to generate ultra-realistic speech from text. You can also clone voice and use them to generate speech. 

<br><br>

> ## 💡 New docs - https://docs.play.ht!
> 
> <div align="center">
>
> <br>
> We are excited to announce the release of our new documentation page, which provides a comprehensive specification of all our API endpoints, along with interactive demos to help you get started quickly.
>
> You can access the documentation by visiting https://docs.play.ht!
> <br><br>
>
> ![image](https://user-images.githubusercontent.com/5091082/232076784-6496568d-afc1-452b-9e57-2f9e2f7ec9f0.png)
> <br>https://docs.play.ht
> </div>
> 
> <br><br>

<br><br>

Take a look at the [Voices reference file](Voices.md) to see a list of the available voices and languages. The file also contains audio samples to help you pick.

**Note:** You need to have a Play.ht account with word credit to be able to access the API.

## Overview of API

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

There are two endpoints on the API that you will use to convert text to speech:
1. `/convert`: Performs the text-to-speech conversion.
2. `/articleStatus`: Lets you know if the conversion is done.

Since the text-to-speech conversion is an asynchronous process, you will first make a `POST` request to the `/convert` endpoint with the text and voice, and then make `GET` requests to the `/articleStatus` endpoint to check if the conversion is done and to get the audio file.

The two endpoints have been described in detail below.

But first, we need authentication!

## Authentication

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

All endpoints require authentication. Authentication consists of two required HTTPS headers:
- `Authorization`: This is where your secret key goes.
- `X-User-ID`: This is where your Play.ht user ID goes.

To access your credentials, make sure you're logged-in to your Play.ht account, then visit your [API Access page](https://play.ht/app/api-access). If you're having issues, you can reach out to us on `support [at] play.ht`.

Make sure to store your secret key privately and do not share it. Never use your secret key in the front-end part of your app or in the browser.

## Endpoints

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

- Base URL: `https://play.ht/api/v1/`

**Notes:**
- All endpoints are relative to the base URL.
- Requests should always be in JSON format, with a `Content-Type: application/json` header.

### Convert text to speech

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

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
    "trimSilence": boolean,   // Optional
    "transcriptionId": string  // Optional - use it to update the same audio file
  }
  ```

  `voice` is the ID of the voice used to synthesize the text. Refer to the [Voices reference file](Voices.md) for more details.
  
  > 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

  Only one of `content` or `ssml` can be passed:

    - `content` is an array of strings, where each string represents a paragraph in plain text format.

    - `ssml` is an array of strings, where each string represents a paragraph in SSML format. [Learn more about SSML](https://www.w3.org/TR/speech-synthesis/). Not all SSML features are supported with all voices.

  `title` is a field to name your file. You can use this name to find the audio in your Play.ht dashboard.
  
  `narrationStyle` is a string representing the tone and accent of the voice to read the text. Make sure the value for `narrationStyle` is supported by the voice in your request. Refer to the [Voices reference file](Voices.md) for more details.

  `globalSpeed` is a string in the format `<number>%`, where `<number>` is in the closed interval of `[20, 200]`. Use this to speed-up, or slow-down the speaking rate of the speech.

  `pronunciations` is an array of key-value pair objects, where `key` is the source string (e.g. `"Play.ht"`), and `value` is the target pronunciation (e.g. `"Play dot H T"`). Use this when you want to customize the pronunciation of a certain word/phrase (e.g. your brand name).

  `trimSilence` is a boolean value. When enabled, the audio will be trimmed to remove any silence from the end of the file.
  
  `transcriptionId` - Pass this to update an existing audio file

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

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

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

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht

Optional fields are only provided when applicable.

- Examples (cURL Request):
  ```ssml with pauses
  curl --location --request POST 'https://play.ht/api/v1/convert' \
  --header 'Authorization: **************' \
  --header 'X-User-ID: ***************' \
  --header 'Content-Type: application/json' \
  --data-raw '{
  "voice": "en-US-MichelleNeural",
  "ssml": ["<speak><p>Hello my fried <break time=\"0.5s\"/></p></speak>"],
  "title": "Testing public api convertion"
  }'
  ```
- Example (Python Request):
  ```
  import requests
  import json

  url = "https://play.ht/api/v1/convert"

  payload = json.dumps({
    "voice": "en-US-MichelleNeural",
    "content": [
     "Hello My frinedsss",
     "either pass content s an array of strings , or ssml , but not both"
    ],
     "title": "Testing public api convertion"
  })
  headers = {
    'Authorization': '******************',
    'X-User-ID': '**************',
    'Content-Type': 'application/json'
  }

  response = requests.request("POST", url, headers=headers, data=payload)
  print(response.text)
  ```
- Example (PHP - pecl_http):
  ```Using multi voices 
  <?php
  $client = new http\Client;
  $request = new http\Client\Request;
  $request->setRequestUrl('https://play.ht/api/v1/convert');
  $request->setRequestMethod('POST');
  $body = new http\Message\Body;
  $body->append('{
    "voice": "en-US-MichelleNeural",
    "ssml": ["<speak><p>Hello my fried <break time=\"0.5s\"/></p></speak>",
        "<speak><p>--BEGIN-PLAYHT-CUSTOM-VOICE--en-US-AmberNeural--END-PLAYHT-CUSTOM-VOICE--How are you doing</p></speak>"],
    "title": "Testing public api convertion"
  }');
  $request->setBody($body);
  $request->setOptions(array());
  $request->setHeaders(array(
    'Authorization' => '*******************',
    'X-User-ID' => '***************',
    'Content-Type' => 'application/json'
  ));
  $client->enqueue($request)->send();
  $response = $client->getResponse();
  echo $response->getBody();
  ```

> 💡 This page is now archived for historic purposes. If you're looking for the most up-to-date information and documentation, please visit our new docs: https://docs.play.ht
