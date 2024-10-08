---
layout: default
title: Voice Recognition API
nav_order: 1
parent: API Reference
ancestor: Technical Documentation
---

# Voice Recognition API Reference

This API reference provides technical details of how the Voice Command service interacts with the Voice Recognition backend, processes voice inputs, converts them into text, and integrates with downstream services like the Google Voice Command NLP engine for further action.

{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

- TOC
{:toc}

### Recognize

```curl
POST /voice/recognize
```

This endpoint is used to send a voice recording and receive the transcribed text. It utilizes Google’s voice recognition system to process the voice input and return it as a structured text response.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: multipart/form-data

#### Request Body

```json
{
  "userId": "<Google Account ID>",
  "audioFile": "<binary audio data>",
  "language": "en-US",
  "context": {
    "application": "gmail",
    "commandExpected": true
  }
}
```

|Parameter|Description|
|-|-|
|userId| Google account ID of the user issuing the command.|
|audioFile| Binary data of the recorded voice input (accepted in formats such as WAV, MP3).|
|language| The language of the user’s voice input (default is "en-US" for English).|
|context| Application-specific context, such as whether a command is expected (e.g., Gmail command).|

#### Response

```json
{
  "status": "success",
  "transcript": "Compose email to John",
  "confidenceScore": 0.98,
  "languageDetected": "en-US",
  "audioDuration": 4.5,
  "voiceMetadata": {
    "noiseLevel": "low",
    "speechRate": "normal"
  }
}
```

|Parameter|Description|
|-|-|
|status| Status of the recognition process (e.g., success, error).|
|transcript| The transcribed text from the voice input.|
|confidenceScore| A score (0.0 to 1.0) representing the confidence level of the transcription accuracy.|
|languageDetected| Detected language of the voice input.|
|audioDuration| The length of the voice input in seconds.|
|voiceMetadata| Additional information about the audio, such as noise levels or speech rate.|

### Voice Streaming

```curl
POST /voice/startStreaming
```

This endpoint is used to start streaming voice input in real-time for transcription. It’s useful when you need to handle continuous voice input, such as in scenarios where commands are spoken over an extended period.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: multipart/form-data

#### Request Body

```json
{
  "userId": "<Google Account ID>",
  "streamFormat": "raw",
  "language": "en-US",
  "context": {
    "application": "gmail"
  }
}
```

|Parameter|Description|
|-|-|
|userId| Google account ID of the user issuing the voice command.|
|streamFormat| Format of the audio stream (e.g., raw, opus).|
|language| The language of the voice input (default is "en-US" for English).|
|context| Application context, such as Gmail or Calendar.|

#### Response

```json
{
  "status": "streamingStarted",
  "streamId": "abc123",
  "languageDetected": "en-US"
}
```

|Parameter|Description|
|-|-|
|status| Indicates that the audio stream has started successfully|
|streamId| Unique identifier for the streaming session.|
|languageDetected| The detected language based on the input stream.|

### End Voice Streaming

```curl
POST /voice/stopStreaming
```

This endpoint stops the voice input stream started by `/voice/startStreaming` and retrieves the transcribed text.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: application/json

#### Request Body

```json
{
  "userId": "<Google Account ID>",
  "streamId": "abc123"
}
```

|Parameter|Description|
|-|-|
|userId| Google account ID of the user issuing the voice command.|
|streamId| The unique identifier of the streaming session (received from /voice/startStreaming).|

#### Response

```curl
{
  "status": "success",
  "transcript": "Send email to Jane",
  "confidenceScore": 0.95,
  "languageDetected": "en-US",
  "audioDuration": 8.5,
  "voiceMetadata": {
    "backgroundNoiseLevel": "medium",
    "speechClarity": "high"
  }
}
```

|Parameter|Description|
|-|-|
|status| Status of the stream closure and transcription.|
|transcript| The final transcription of the audio stream.
|confidenceScore| The system’s confidence in the accuracy of the transcription.|
|languageDetected| The detected language of the input.
|audioDuration| Duration of the total streamed audio in seconds.|
|voiceMetadata| Additional metadata about the voice input, including noise levels and speech clarity.|

### Detect Language

```curl
POST /voice/detectLanguage
```

This endpoint detects the language from a short voice sample to ensure that the voice command is processed in the correct language.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: multipart/form-data

#### Request Body

```json
{
  "userId": "<Google Account ID>",
  "audioSample": "<binary audio data>",
  "sampleDuration": 5
}
```

|Parameter|Description|
|-|-|
|userId| The Google account ID of the user submitting the sample.|
|audioSample| Binary data of the short audio sample used for language detection (e.g., WAV, MP3).|
|sampleDuration| Duration of the voice sample in seconds (minimum 3 seconds recommended).|

#### Response

```json
{
  "status": "success",
  "detectedLanguage": "en-US",
  "confidenceScore": 0.96
}
```

|Parameter|Description|
|-|-|
|status| Status of the language detection process.
|detectedLanguage| The language detected from the voice sample (e.g., "en-US", "es-ES").|
|confidenceScore| Confidence level of the detected language.|

### Get Supported Languages

```curl
GET /voice/getSupportedLanguages
```

This endpoint retrieves a list of languages supported by the Google Voice Recognition service, allowing applications to provide multi-language support.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`

#### Response

```json
{
  "status": "success",
  "supportedLanguages": [
    "en-US",
    "es-ES",
    "fr-FR",
    "de-DE",
    "ja-JP"
  ]
}
```

|Parameter|Description|
|-|-|
|status| Status of the Request.|
|supportedLanguages| An array of language codes supported by the voice recognition service.|

## Usage Example

Example: Recognizing Voice Input for Composing an Email

### Step 1: Send Audio for Transcription

1. The user provides a voice input such as *“Compose email to Jane about the project.”*
2. The service submits the audio file to `/voice/recognize`.

```json
{
  "userId": "user123",
  "audioFile": "<binary audio data>",
  "language": "en-US"
}
```

### Step 2: Get the Transcription

The response from the voice recognition backend returns the transcribed text and metadata.

```json
{
  "status": "success",
  "transcript": "Compose email to Jane about the project",
  "confidenceScore": 0.98,
  "languageDetected": "en-US"
}
```

### Step 3: Process with NLP

After receiving the transcribed command, the service sends it to the NLP backend for further processing using /nlp/interpretCommand.
