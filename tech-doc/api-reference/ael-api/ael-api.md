---
layout: default
title: Action Execution Layer API
nav_order: 3
parent: API Reference
ancestor: Technical Documentation
---

## API Endpoints

The integration heavily relies on the Gmail REST API, which offers a set of endpoints for email management, search, and composition. Below is a list of relevant API endpoints used in Google Voice Command:

### Send Email

#### POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/send

##### Description

Sends an email using the Gmail API.

###### Usage

Triggered when the user gives a “Send email” voice command after composing an email.

##### #### Request Example

```
POST https://gmail.googleapis.com/gmail/v1/users/me/messages/send
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "raw": "{base64-encoded-email}"
}
```

### Get Email by ID

	•	Endpoint: GET https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}
	•	Description: Retrieves a specific email message by its ID. Used when the user Requests to read a specific email.

#### Request:

GET https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}
Authorization: Bearer {access_token}

Example Usage (via Voice Command):

	•	User says, “Read my latest email.”
	•	The most recent email’s ID is retrieved from the inbox and passed into the messages/{messageId} endpoint to fetch email details (subject, body, etc.).

### List Emails (Inbox Search)

	•	Endpoint: GET https://gmail.googleapis.com/gmail/v1/users/{userId}/messages?q={query}
	•	Description: Searches the user’s inbox based on a query string.

#### Request:

GET https://gmail.googleapis.com/gmail/v1/users/me/messages?q=from:john@example.com
Authorization: Bearer {access_token}

Example Usage (via Voice Command):

	•	User says, “Search for emails from John.”
	•	The query "from:john@example.com" is passed to the q parameter, and the #### Responsereturns all matching messages from the inbox.

### Label Email

	•	Endpoint: POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}/modify
	•	Description: Applies or removes labels from a message. Triggered by the “Label this email as [label]” command.

#### Request:

POST https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}/modify
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "addLabelIds": ["{labelId}"]
}

Example Usage (via Voice Command):

	•	User says, “Label this email as Work.”
	•	The system retrieves the label ID for “Work” and applies it to the selected message by calling the modify endpoint.

### Mark Email as Important

	•	Endpoint: POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}/modify
	•	Description: Marks a message as important by adding the "IMPORTANT" label.

#### Request:

POST https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}/modify
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "addLabelIds": ["IMPORTANT"]
}

Example Usage (via Voice Command):

	•	User says, “Mark this email as important.”
	•	The email is marked by adding the “IMPORTANT” label via the modify endpoint.