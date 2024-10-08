---
layout: default
title: NLP and AEL API
nav_order: 2
parent: API Reference
ancestor: Technical Documentation
---

# Voice Command NLP and Action Execution Layer API Reference
{: .no_toc }

This API reference provides the API endpoints, request parameters, and response structures for developers integrating Google Voice Command with the Gmail NLP backend, enabling a seamless hands-free email experience. These APIs enable voice processing, command interpretation, and email management within Gmail through voice commands.

## Table of Contents
{: .no_toc .text-delta }

- TOC
{:toc}

### Intepret Command

```curl
POST /nlp/interpretCommand
```

This endpoint processes a voice input to interpret the user’s command and maps it to a corresponding email action.

#### Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: application/json

#### Request Body

```json
{
  "voiceInput": "<transcribed voice command>",
  "userId": "<Google Account ID>",
  "context": {
    "application": "gmail",
    "conversationState": "<current state of conversation>",
    "emailContext": {
      "threadId": "<current thread ID>",
      "label": "<current label>",
      "emailId": "<email ID if command refers to a specific email>"
    }
  }
}
```

|Parameter|Description|
|-|-|
|voiceInput| Transcribed text of the user’s voice command.|
|userId| Google account ID of the user issuing the command.|
|context| Includes the current context of the conversation, such as the application state (Gmail), and details about the email or thread the command applies to.|

#### Response

```json
{
  "status": "success",
  "commandType": "composeEmail",
  "action": {
    "description": "Compose email to John",
    "parameters": {
      "recipient": "john.doe@example.com",
      "subject": "Project Update",
      "messageBody": "Please provide an update by tomorrow."
    }
  }
}
```

|Parameter|Description|
|-|-|
|status| Status of the command interpretation (e.g., success, error).|
|commandType| The type of email-related action interpreted (e.g., composeEmail, readEmail, labelEmail).|
|action| The specific action to be performed, including relevant parameters.|

### Execute Command

```curl
POST /nlp/executeCommand
```

Once a command is interpreted, this endpoint executes the email action, such as sending an email, applying a label, or reading the email content.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: application/json

#### Request Body

```json
{
  "userId": "<Google Account ID>",
  "commandType": "composeEmail",
  "parameters": {
    "recipient": "jane.doe@example.com",
    "subject": "Meeting Update",
    "messageBody": "The meeting is postponed to next Monday.",
    "threadId": "<optional: thread ID for replies or forwards>"
  }
}
```

|Parameter|Description|
|-|-|
|userId| The Google account ID of the user performing the action.|
|commandType| The type of action to be executed (e.g., composeEmail, sendEmail, markImportant).|
|parameters| Parameters required to complete the action, such as recipient, subject, and message body for composing emails.|

#### Response

```json
{
  "status": "success",
  "emailId": "<generated email ID>",
  "actionSummary": "Email composed and ready to send"
}
```

|Parameter|Description|
|-|-|
|status| Status of the action execution (e.g., success, failure).|
|emailId| ID of the newly composed email, if applicable.|
|actionSummary| Summary of the action performed.|

### Get Email Details

```curl
GET /nlp/getEmailDetails
```

This endpoint retrieves email details based on the user’s command, such as reading an email or checking the latest email in the inbox.

#### Request Headers

- Authorization: Bearer `<OAuth2 token>`

#### Request Body

```json
{
  "userId": "<Google Account ID>",
  "emailId": "<ID of the email to retrieve>"
}
```

|Parameter|Description|
|-|-|
|userId| The Google account ID of the user requesting email details.|
|emailId| The ID of the specific email to be retrieved.|

#### Response

```json
{
  "status": "success",
  "email": {
    "emailId": "<email ID>",
    "sender": "john.doe@example.com",
    "subject": "Meeting Agenda",
    "messageBody": "Please find attached the agenda for tomorrow's meeting.",
    "receivedDate": "2024-10-05T12:30:00Z"
  }
}
```

|Parameter|Description|
|-|-|
|status| Status of the retrieval (e.g., success, failure).|
|email| Contains the email details including the sender, subject, message body, and the date received.|

### Update Email Status

```curl
POST /nlp/updateEmailStatus
```

This endpoint updates the status of an email, such as marking it as important, archiving it, or applying a label.

### Request Headers

- Authorization: Bearer `<OAuth2 token>`
- Content-Type: application/json

### Request Body

```json
{
  "userId": "<Google Account ID>",
  "emailId": "<email ID>",
  "statusUpdate": {
    "action": "markImportant",
    "label": "Work"
  }
}
```

|Parameter|Description|
|-|-|
|userId| The Google account ID of the user updating the email status.|
|emailId| The ID of the email whose status is being updated.|
|statusUpdate| The specific action to update the email’s status, such as “markImportant”, “archive”, or “label”.|

### Response

```json
{
  "status": "success",
  "updatedEmailId": "<email ID>",
  "actionSummary": "Email marked as important and labeled Work"
}
```

|Parameter|Description|
|-|-|
|status| Status of the update (e.g., success, failure).|
|updatedEmailId| The ID of the email that was updated.|
|actionSummary| A summary of the action performed on the email.|

## API Usage Example

Example: Composing and Sending an Email via Voice Command

### Step 1. Interpret the Command

1. Voice Recognition API provides command string: “*Compose email to John about the meeting.*”
2. The service sends a request to `/nlp/interpretCommand` with the transcribed command.

```json
{
  "voiceInput": "Compose email to John about the meeting",
  "userId": "user123",
  "context": {
    "application": "gmail"
  }
}
```

### Step 2. Execute the Command

After interpreting the command, send a request to `/nlp/executeCommand` to compose and send the email.

```json
{
  "userId": "user123",
  "commandType": "composeEmail",
  "parameters": {
    "recipient": "john.doe@example.com",
    "subject": "Meeting Discussion",
    "messageBody": "Let’s discuss the meeting agenda tomorrow."
  }
}
```

The request is then passed to the Gmail servers to complete the email delivery.
