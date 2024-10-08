---
layout: default
title: Archiving
nav_order: 4
parent: Commands
ancestor: User Guide
---

## API Reference for Voice Recognition and NLP

This section provides technical details for developers on how the Google Voice Recognition backend processes voice inputs, converts them into text, and integrates with downstream services like the Google Voice Command NLP engine for further action. The APIs outlined here enable developers to handle the voice-to-text conversion and retrieve necessary metadata for context-sensitive commands.

1. POST /voice/recognize

This endpoint is used to send a voice recording and receive the transcribed text. It utilizes Google’s voice recognition system to process the voice input and return it as a structured text #### Response

	•	URL: https://api.google.com/voice/recognize
	•	Method: POST

#### Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: multipart/form-data

#### Request Body:

{
  "userId": "<Google Account ID>",
  "audioFile": "<binary audio data>",
  "language": "en-US",
  "context": {
    "application": "gmail",
    "commandExpected": true
  }
}

	•	userId: Google account ID of the user issuing the command.
	•	audioFile: Binary data of the recorded voice input (accepted in formats such as WAV, MP3).
	•	language: The language of the user’s voice input (default is "en-US" for English).
	•	context: Application-specific context, such as whether a command is expected (e.g., Gmail command).

#### Response

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

	•	status: Status of the recognition process (e.g., success, error).
	•	transcript: The transcribed text from the voice input.
	•	confidenceScore: A score (0.0 to 1.0) representing the confidence level of the transcription accuracy.
	•	languageDetected: Detected language of the voice input.
	•	audioDuration: The length of the voice input in seconds.
	•	voiceMetadata: Additional information about the audio, such as noise levels or speech rate.

2. POST /voice/startStreaming

This endpoint is used to start streaming voice input in real-time for transcription. It’s useful when you need to handle continuous voice input, such as in scenarios where commands are spoken over an extended period.

	•	URL: https://api.google.com/voice/startStreaming
	•	Method: POST

#### Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: multipart/form-data

#### Request Body:

{
  "userId": "<Google Account ID>",
  "streamFormat": "raw",
  "language": "en-US",
  "context": {
    "application": "gmail"
  }
}

	•	userId: Google account ID of the user issuing the voice command.
	•	streamFormat: Format of the audio stream (e.g., raw, opus).
	•	language: The language of the voice input (default is "en-US" for English).
	•	context: Application context, such as Gmail or Calendar.

#### Response

{
  "status": "streamingStarted",
  "streamId": "abc123",
  "languageDetected": "en-US"
}

	•	status: Indicates that the audio stream has started successfully.
	•	streamId: Unique identifier for the streaming session.
	•	languageDetected: The detected language based on the input stream.

3. POST /voice/stopStreaming

This endpoint stops the voice input stream started by /voice/startStreaming and retrieves the transcribed text.

	•	URL: https://api.google.com/voice/stopStreaming
	•	Method: POST

#### Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: application/json

#### Request Body:

{
  "userId": "<Google Account ID>",
  "streamId": "abc123"
}

	•	userId: Google account ID of the user issuing the voice command.
	•	streamId: The unique identifier of the streaming session (received from /voice/startStreaming).

#### Response

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

	•	status: Status of the stream closure and transcription.
	•	transcript: The final transcription of the audio stream.
	•	confidenceScore: The system’s confidence in the accuracy of the transcription.
	•	languageDetected: The detected language of the input.
	•	audioDuration: Duration of the total streamed audio in seconds.
	•	voiceMetadata: Additional metadata about the voice input, including noise levels and speech clarity.

4. POST /voice/detectLanguage

This endpoint detects the language from a short voice sample to ensure that the voice command is processed in the correct language.

	•	URL: https://api.google.com/voice/detectLanguage
	•	Method: POST

#### Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: multipart/form-data

#### Request Body:

{
  "userId": "<Google Account ID>",
  "audioSample": "<binary audio data>",
  "sampleDuration": 5
}

	•	userId: The Google account ID of the user submitting the sample.
	•	audioSample: Binary data of the short audio sample used for language detection (e.g., WAV, MP3).
	•	sampleDuration: Duration of the voice sample in seconds (minimum 3 seconds recommended).

#### Response

{
  "status": "success",
  "detectedLanguage": "en-US",
  "confidenceScore": 0.96
}

	•	status: Status of the language detection process.
	•	detectedLanguage: The language detected from the voice sample (e.g., "en-US", "es-ES").
	•	confidenceScore: Confidence level of the detected language.

5. GET /voice/getSupportedLanguages

This endpoint retrieves a list of languages supported by the Google Voice Recognition service, allowing applications to provide multi-language support.

	•	URL: https://api.google.com/voice/getSupportedLanguages
	•	Method: GET

#### Request Headers:

	•	Authorization: Bearer <OAuth2 token>

#### Response

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

	•	status: Status of the Request.
	•	supportedLanguages: An array of language codes supported by the voice recognition service.

Usage Example

Example: Recognizing Voice Input for Composing an Email

	1.	Step 1: Send Audio for Transcription
	•	The user provides a voice input such as “Compose email to Jane about the project.”
	•	Submit the audio file to /voice/recognize.

{
  "userId": "user123",
  "audioFile": "<binary audio data>",
  "language": "en-US"
}

	2.	Step 2: Get the Transcription
	•	The #### Responsefrom the voice recognition backend returns the transcribed text and metadata.

{
  "status": "success",
  "transcript": "Compose email to Jane about the project",
  "confidenceScore": 0.98,
  "languageDetected": "en-US"
}

	3.	Step 3: Process with NLP
	•	After receiving the transcribed command, send it to the NLP backend for further processing using /nlp/interpretCommand.

This API reference provides a detailed overview of how to interface with the Google Voice Recognition backend, enabling developers to convert voice inputs into actionable text for use in Google services like Gmail.