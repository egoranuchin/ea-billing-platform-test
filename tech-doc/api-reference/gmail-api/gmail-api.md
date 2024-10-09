---
layout: default
title: Gmail API Integration
nav_order: 3
parent: API Reference
ancestor: Technical Documentation
---

# Gmail API Integration Reference
{: .no_toc }

The integration heavily relies on the Gmail REST API, which offers a set of endpoints for email management, search, and composition. Below is a list of relevant Gmail API endpoints used in Voice Command.

For more information about the Gmail API, please refer to the [Google Documentation](https://developers.google.com/gmail/api/guides).

## Table of Contents
{: .no_toc .text-delta }

- TOC
{:toc}

### Send Email

```
POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/send
```

##### Description
{: .no_toc }

Sends an email using the Gmail API.

##### Usage
{: .no_toc }

Triggered when the user gives a “*Send email*” voice command after composing an email.

##### Request Example
{: .no_toc }

```
POST https://gmail.googleapis.com/gmail/v1/users/me/messages/send
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "raw": "{base64-encoded-email}"
}
```

### Get Email by ID

```
GET https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}
```

#### Description
{: .no_toc }

Retrieves a specific email message by its ID. Used when the user Requests to read a specific email.

#### Request Example
{: .no_toc }

```
GET https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}
Authorization: Bearer {access_token}
```

#### Example Usage (via Voice Command)
{: .no_toc }

1. User says, “*Read my latest email.*”
2. The most recent email’s ID is retrieved from the inbox and passed into the messages/{messageId} endpoint to fetch email details (subject, body, etc.).

### List Emails (Inbox Search)

```
GET https://gmail.googleapis.com/gmail/v1/users/{userId}/messages?q={query}
```

#### Description
{: .no_toc }

Searches the user’s inbox based on a query string.

#### Request
{: .no_toc }

```
GET https://gmail.googleapis.com/gmail/v1/users/me/messages?q=from:john@example.com
Authorization: Bearer {access_token}
```

#### Example Usage (via Voice Command)
{: .no_toc }

1. User says, “*Search for emails from John.*”
2. The query "from:john@example.com" is passed to the q parameter, and the #### Responsereturns all matching messages from the inbox.

### Label Email

```
POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}/modify
```

#### Description
{: .no_toc }

Applies or removes labels from a message. Triggered by the “Label this email as [label]” command.

#### Request
{: .no_toc }

```
POST https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}/modify
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "addLabelIds": ["{labelId}"]
}
```

#### Example Usage (via Voice Command)
{: .no_toc }

1. User says, “*Label this email as Work.*”
2. The system retrieves the label ID for “Work” and applies it to the selected message by calling the modify endpoint.

### Mark Email as Important

```
POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/{messageId}/modify
```

#### Description
{: .no_toc }

Marks a message as important by adding the "IMPORTANT" label.

#### Request Example
{: .no_toc }

```
POST https://gmail.googleapis.com/gmail/v1/users/me/messages/{messageId}/modify
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "addLabelIds": ["IMPORTANT"]
}
```

#### Example Usage (via Voice Command)
{: .no_toc }

1. User says, *“Mark this email as important.”*
2. The email is marked by adding the “IMPORTANT” label via the modify endpoint.
