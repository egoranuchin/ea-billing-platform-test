---
layout: default
title: API Reference
parent: Technical Documentation
---

## Introduction

The Google Voice Command feature for Gmail enhances email management by providing hands-free control. By utilizing the Gmail API for backend processing and voice recognition for front-end commands, developers can extend or integrate this functionality into various applications. This document should serve as a reference for API usage and command execution flow.

For additional details on the Gmail API, refer to the Gmail API documentation.

Authentication

Google Voice Command uses OAuth 2.0 to authenticate users with the Gmail API. Each command executed requires an active OAuth token from the user. Here’s how to handle the authentication flow:

1. OAuth 2.0 Flow

	•	Obtain an OAuth token by redirecting users to the following URL:

https://accounts.google.com/o/oauth2/auth
?client_id={client_id}
&redirect_uri={redirect_uri}
&response_type=code
&scope=https://www.googleapis.com/auth/gmail.modify

	•	Once users grant access, exchange the authorization code for an access token using:

POST https://oauth2.googleapis.com/token

	•	Use the access_token in the Authorization header for each API request:

Authorization: Bearer {access_token}

Command Mapping and NLP Integration

The NLP system integrated with Google Voice Command converts user input into structured Gmail actions. Each voice command is mapped to one or more Gmail API calls. For example:

	•	User command: “Send an email to [name]”
	•	NLP breakdown:
	•	Intent: Send Email
	•	Entity: Recipient’s name/email address
	•	Action: POST messages/send

Error Handling and Response Management

	•	Error Responses: Gmail API errors such as rate limits, invalid email addresses, or authentication issues are captured and relayed back to the user through voice responses (e.g., “Unable to send the email at this time, please try again later”).
	•	Timeouts: If no command is detected within a preset timeout period, the system can prompt users to retry.

## API Endpoints

The integration heavily relies on the Gmail REST API, which offers a set of endpoints for email management, search, and composition. Below is a list of relevant API endpoints used in Google Voice Command:

1. Send Email

	•	Endpoint: POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/send
	•	Description: Sends an email using the Gmail API.
	•	Usage: Triggered when the user gives a “Send email” voice command after composing an email.

Request:

POST https://gmail.googleapis.com/gmail/v1/users/me/messages/send
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "raw": "{base64-encoded-email}"
}

Example Usage (via Voice Command):

	•	User says, “Send an email to john@example.com.”
	•	Voice Recognition parses the command, retrieves the email content (subject, body) and encodes it in Base64.
	•	The messages/send endpoint is called to send the email.

2. Get Email by ID

	•	Endpoint: GET https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}
	•	Description: Retrieves a specific email message by its ID. Used when the user requests to read a specific email.

Request:

GET https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}
Authorization: Bearer {access_token}

Example Usage (via Voice Command):

	•	User says, “Read my latest email.”
	•	The most recent email’s ID is retrieved from the inbox and passed into the messages/{messageId} endpoint to fetch email details (subject, body, etc.).

3. List Emails (Inbox Search)

	•	Endpoint: GET https://gmail.googleapis.com/gmail/v1/users/{userId}/messages?q={query}
	•	Description: Searches the user’s inbox based on a query string.

Request:

GET https://gmail.googleapis.com/gmail/v1/users/me/messages?q=from:john@example.com
Authorization: Bearer {access_token}

Example Usage (via Voice Command):

	•	User says, “Search for emails from John.”
	•	The query "from:john@example.com" is passed to the q parameter, and the response returns all matching messages from the inbox.

4. Label Email

	•	Endpoint: POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}/modify
	•	Description: Applies or removes labels from a message. Triggered by the “Label this email as [label]” command.

Request:

POST https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}/modify
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "addLabelIds": ["{labelId}"]
}

Example Usage (via Voice Command):

	•	User says, “Label this email as Work.”
	•	The system retrieves the label ID for “Work” and applies it to the selected message by calling the modify endpoint.

5. Mark Email as Important

	•	Endpoint: POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}/modify
	•	Description: Marks a message as important by adding the "IMPORTANT" label.

Request:

POST https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}/modify
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "addLabelIds": ["IMPORTANT"]
}

Example Usage (via Voice Command):

	•	User says, “Mark this email as important.”
	•	The email is marked by adding the “IMPORTANT” label via the modify endpoint.
