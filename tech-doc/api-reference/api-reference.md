---
layout: default
title: API Reference
parent: Technical Documentation
has_children: true
---

# Voice Command API Reference
{: .no_toc }

## Introduction
{: .no_toc }

The Voice Command feature for Gmail enhances email management by utilizing the Gmail API for backend processing and voice recognition for front-end commands. Developers can extend or integrate this functionality into various applications. This document should serve as a reference for API usage and command execution flow.

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

### Authentication

Voice Command uses OAuth 2.0 to authenticate users with the Google services API. Each command executed requires an active OAuth token from the user.

#### OAuth 2.0 Flow
{: .no_toc }

1. Voice Command obtains an OAuth token by redirecting users to the following URL:

    ```
    https://accounts.google.com/o/oauth2/auth?client_id={client_id}&redirect_uri={redirect_uri}&#### Responsetype=code&scope=https://www.googleapis.com/auth/gmail.modify
    ```

2. Once users grant access, the service exchanges the authorization code for an access token using:

    ```
    POST https://oauth2.googleapis.com/token
    ```

3. The `access_token` in the Authorization header is used for each API Request:

    ```json
    Authorization: Bearer {access_token}
    ```

### Command Mapping and NLP Integration

The NLP system integrated with Voice Command converts user input into structured Gmail actions. Each voice command is mapped to one or more Gmail API calls. For example:

**User command**: “*Send an email to [name]*”

**NLP breakdown**:

- Intent: Send Email
- Entity: Recipient’s name/email address
- Action: POST messages/send

For more information about the Voice Recognition API, see the [Voice Recognition API Reference]().

For more information about the NLP API, see the [NLP AEL API Reference]().

For more information about the Gmail API integration, see the [Gmail API Integration Reference]().

### Error Handling and Response Management

|Error|Handling|
|-|-|
|Error Response| Gmail API errors such as rate limits, invalid email addresses, or authentication issues are captured and relayed back to the user through voice response (e.g., “Unable to send the email at this time, please try again later”).|
|Timeouts| If no command is detected within a preset timeout period, the system can prompt users to retry.|
