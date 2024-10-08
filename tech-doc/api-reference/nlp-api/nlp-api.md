API Reference for Google Voice Command NLP Backend

This section outlines the API endpoints, request parameters, and response structures for developers integrating Google Voice Command with the Gmail NLP backend. These APIs enable voice processing, command interpretation, and email management within Gmail through voice commands.

1. POST /nlp/interpretCommand

This endpoint processes a voice input to interpret the user’s command and maps it to a corresponding email action.

	•	URL: https://api.google.com/nlp/interpretCommand
	•	Method: POST

Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: application/json

Request Body:

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

	•	voiceInput: Transcribed text of the user’s voice command.
	•	userId: Google account ID of the user issuing the command.
	•	context: Includes the current context of the conversation, such as the application state (Gmail), and details about the email or thread the command applies to.

Response:

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

	•	status: Status of the command interpretation (e.g., success, error).
	•	commandType: The type of email-related action interpreted (e.g., composeEmail, readEmail, labelEmail).
	•	action: The specific action to be performed, including relevant parameters.

2. POST /nlp/executeCommand

Once a command is interpreted, this endpoint executes the email action, such as sending an email, applying a label, or reading the email content.

	•	URL: https://api.google.com/nlp/executeCommand
	•	Method: POST

Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: application/json

Request Body:

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

	•	userId: The Google account ID of the user performing the action.
	•	commandType: The type of action to be executed (e.g., composeEmail, sendEmail, markImportant).
	•	parameters: Parameters required to complete the action, such as recipient, subject, and message body for composing emails.

Response:

{
  "status": "success",
  "emailId": "<generated email ID>",
  "actionSummary": "Email composed and ready to send"
}

	•	status: Status of the action execution (e.g., success, failure).
	•	emailId: ID of the newly composed email, if applicable.
	•	actionSummary: Summary of the action performed.

3. GET /nlp/getEmailDetails

This endpoint retrieves email details based on the user’s command, such as reading an email or checking the latest email in the inbox.

	•	URL: https://api.google.com/nlp/getEmailDetails
	•	Method: GET

Request Headers:

	•	Authorization: Bearer <OAuth2 token>

Request Parameters:

userId=<Google Account ID>
emailId=<ID of the email to retrieve>

	•	userId: The Google account ID of the user requesting email details.
	•	emailId: The ID of the specific email to be retrieved.

Response:

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

	•	status: Status of the retrieval (e.g., success, failure).
	•	email: Contains the email details including the sender, subject, message body, and the date received.

4. POST /nlp/updateEmailStatus

This endpoint updates the status of an email, such as marking it as important, archiving it, or applying a label.

	•	URL: https://api.google.com/nlp/updateEmailStatus
	•	Method: POST

Request Headers:

	•	Authorization: Bearer <OAuth2 token>
	•	Content-Type: application/json

Request Body:

{
  "userId": "<Google Account ID>",
  "emailId": "<email ID>",
  "statusUpdate": {
    "action": "markImportant",
    "label": "Work"
  }
}

	•	userId: The Google account ID of the user updating the email status.
	•	emailId: The ID of the email whose status is being updated.
	•	statusUpdate: The specific action to update the email’s status, such as “markImportant”, “archive”, or “label”.

Response:

{
  "status": "success",
  "updatedEmailId": "<email ID>",
  "actionSummary": "Email marked as important and labeled Work"
}

	•	status: Status of the update (e.g., success, failure).
	•	updatedEmailId: The ID of the email that was updated.
	•	actionSummary: A summary of the action performed on the email.

API Usage Example

Example: Composing and Sending an Email via Voice Command

	1.	Interpret the Command:
	•	User says: “Compose email to John about the meeting.”
	•	Send a request to /nlp/interpretCommand with the transcribed command.

{
  "voiceInput": "Compose email to John about the meeting",
  "userId": "user123",
  "context": {
    "application": "gmail"
  }
}

	2.	Execute the Command:
	•	After interpreting the command, send a request to /nlp/executeCommand to compose and send the email.

{
  "userId": "user123",
  "commandType": "composeEmail",
  "parameters": {
    "recipient": "john.doe@example.com",
    "subject": "Meeting Discussion",
    "messageBody": "Let’s discuss the meeting agenda tomorrow."
  }
}

This API reference provides the core details for developers to integrate and work with the Google Voice Command NLP backend for Gmail, enabling a seamless hands-free email experience.