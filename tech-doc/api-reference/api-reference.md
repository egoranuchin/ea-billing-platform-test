---
layout: default
title: API Reference
parent: Technical Documentation
has_children: true
---

# Voice Command API Reference

## Introduction

The Voice Command feature for Gmail enhances email management by utilizing the Gmail API for backend processing and voice recognition for front-end commands. Developers can extend or integrate this functionality into various applications. This document should serve as a reference for API usage and command execution flow.

### Authentication

Voice Command uses OAuth 2.0 to authenticate users with the Gmail API. Each command executed requires an active OAuth token from the user.

#### OAuth 2.0 Flow

1. Obtain an OAuth token by redirecting users to the following URL:

``https://accounts.google.com/o/oauth2/auth?client_id={client_id}&redirect_uri={redirect_uri}&#### Responsetype=code&scope=https://www.googleapis.com/auth/gmail.modify``

2. Once users grant access, exchange the authorization code for an access token using:

``POST https://oauth2.googleapis.com/token``

3. Use the `access_token` in the Authorization header for each API Request:

``Authorization: Bearer {access_token}``

### Command Mapping and NLP Integration

The NLP system integrated with Voice Command converts user input into structured Gmail actions. Each voice command is mapped to one or more Gmail API calls. For example:

User command: “Send an email to [name]”

NLP breakdown:

* Intent: Send Email
* Entity: Recipient’s name/email address
* Action: POST messages/send

### Error Handling and #### ResponseManagement

* Error #### Response: Gmail API errors such as rate limits, invalid email addresses, or authentication issues are captured and relayed back to the user through voice #### Response (e.g., “Unable to send the email at this time, please try again later”).
* Timeouts: If no command is detected within a preset timeout period, the system can prompt users to retry.




## Action Execution Layer





