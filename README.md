# Bright Artistry API Docs

## Table of Contents

1. [Overview](#section-overview)
    1. [Purpose](#section-generalinfo)
    2. [Features](#section-features)
2. [How things work](#section-hiw)
    1. [Escalation](#section-hiw-escalation)
3. [Authentication](#section-authentication)
4. [Endpoints](#section-escalation)
    1. [Get API Status](#section-getstatus)
    2. [Get Organisation Profile](#section-getprofile)
    3. [Get Organisation Members](#section-getmembers)
    4. [Post Escalation](#section-escalate)
    5. [Get Log Information](#section-getloginfo)
5. [API Error Codes](#section-errorcodes)

## <a name="section-overview"></a>Overview

### <a name="section-purpose"></a>Purpose

The Bright Artistry API provides a robust platform for managing and tracking issues within your applications. It offers
features for logging errors, escalating critical issues, and integrating with external tools such as ClickUp for task
management. The API is designed to enhance your operational efficiency by ensuring that critical problems are swiftly
communicated and addressed.

- **Base URL**:
    - Production: `https://api.brightartistry.com`
    - Development: `https://api-dev.brightartistry.com`
- **Request Format**: All endpoints accept a JSON request body unless specified otherwise.
- **Response Format**: All responses, whether errors or successes, follow a consistent structure for easy parsing and
  handling.

### Features

- **Error Logging**
  The API allows you to log errors from your applications, capturing essential details such as error codes, messages,
  severity, and additional information. These logs are stored and can be retrieved for analysis and reporting.

- **Escalation**
  One of the key features of the Bright Artistry API is the ability to escalate critical issues. The escalation process
  ensures that severe problems are not only logged but also brought to the immediate attention of the relevant
  stakeholders.

- **Integration with Task Management platforms**
  For effective task management, the API integrates with ClickUp. When a new major or critical issue is detected, the
  API
  can automatically create tasks in ClickUp, ensuring that your team is aware and can take action promptly.

## <a name="section-hiw"></a>How things work

### <a name="section-hiw-escalation"></a>How Escalation works

Escalation in the Bright Artistry API is designed to prioritise and address critical issues as soon as they occur. The
process involves sending notifications through various channels and creating tasks in ClickUp for thorough tracking and
resolution.

The logical flow for escalation looks something like in, in order:

1. **Logging:** we store the details for future reference
2. **Issue Classification:** we check if there have been similar logs in the past and link this new one to the
   previous under the umbrella of an `Issue`. This is used for summary reports.
3. **Notifications**: Based on the log and your user and organisation settings, we send out messages on various
   channels, including:
   `EMAIL`, `SMS`, `WHATSAPP`.
4. **Task Management**: Based on the log and your organisation settings, we first check if a task related to this log
   exists. If it doesn't, we create a new task

## <a name="section-authentication"></a>Authentication

To ensure secure and authorized access to the API, Bright Artistry uses API key authentication. Each client is provided
with a unique API key that must be included in the headers of every request.

### Obtaining an API Key

To obtain an API key, please contact your BA Rep or the support team at [support@brightartistry.com](). Your request
should include details about your application and the intended use of the API.

#### Test Client and API Key

For developers looking to test and interact with the Bright Artistry API, there is a single example client available.
This allows developers to explore and understand the API functionalities without affecting live data.

Test API Key: To obtain a test API key, you need to contact the Bright Artistry support team at api@brightartistry.com.
Purpose: The test client is designed to help developers familiarize themselves with the API endpoints, test their code,
and ensure everything works as expected before moving to the production environment.
Interactions: With the test API key, you can make requests to the development environment. This provides a realistic
testing scenario while ensuring that no production data is impacted.

### Using the API Key

Include the API key in the `x-api-key` header of your HTTP requests. Here is an example of how to use the API key in a
request:

Example Request Header:

```
x-api-key: your_api_key_here
```

Example usage:

```
curl -X POST https://api.brightartistry.com/profile \
  -H "Content-Type: application/json" \
  -H "x-api-key: your_api_key_here" \
  -d '{
        "name": "Example Company",
      }'
```

## Response format

At Bright Artistry, we aim to provide clear, consistent, and actionable responses to all API requests. This
documentation outlines the structure and content of success and error messages you can expect when interacting with our
API. Understanding these responses will help you handle them effectively in your applications.

### Success Messages

When an API request is successful, the response will always include a code field indicating the HTTP status code and a
data field containing the relevant information. The format of a success message can
vary slightly based on the type of operation but will follow these general structures:

**Key Fields**

- `status`: HTTP status code indicating the success of the operation (typically 201 for resource creation).
- `data`: Contains detailed information about the operation. This is either
    1. A single standard entity containing a `message`
    2. A single entity unique to the endpoint
    3. An array of entities

#### 1. General success message

```json
{
  "status": 201,
  "data": {
    "message": "Welcome"
  }
}
```

#### 2. Single object

```json
{
  "status": 201,
  "data": {
    "name": "test",
    "title": "mr",
    "role": "ceo"
  }
}
```

#### 3. Multiple Objects:

```json
{
  "status": 201,
  "data": [
    {
      "name": "test",
      "title": "mr",
      "role": "ceo"
    },
    {
      "name": "test",
      "title": "mr",
      "role": "ceo"
    },
    {
      "name": "test",
      "title": "mr",
      "role": "ceo"
    }
  ]
}

```

### Error Messages

If an API request fails, the response will include a code field indicating the HTTP status code, along with an error
field that provides details about the failure. The format of an error message is as follows:

**Key Fields**

- `status`: HTTP status code indicating the success of the operation.
- `error`: Contains detailed information about the operation. This contains
    1. `code`: A string enum. You can see a list of these in one of the following sections
    2. `message`: A brief description of the error.
    3. `details`: Additional information about the error, which can help in diagnosing the issue.

```json
{
  "status": 400,
  "error": {
    "code": "MISSING_FIELDS",
    "message": "Bad Request",
    "details": "The provided data is invalid or incomplete."
  }
}

```

## <a name="section-endpoints"></a> Endpoints

--- 

### <a name="section-getstatus"></a> Get API status [GET: /status]

This endpoint retrieves the current status of the API

- **URL:** `/status`
- **Method:** `GET`
- **Authentication:** None

#### Request Body

None

#### Response

A message & code describing the status

```json
{
  "data": {
    "message": "API up and running"
  },
  "status": 200
}

```

--- 

### <a name="section-getprofile"></a>Get Organisation Profile [GET: /profile]

This endpoint retrieves the profile information of the client organisation associated with the provided API key.

- **URL:** `/profile`
- **Method:** `GET`
- **Authentication:** Required (API Key)

#### Request Body

None

#### Response

Returns the organization profile in JSON format.

```json
{
  "status": 200,
  "data": {
    "name": "Acme Inc.",
    "ba_rep": 5654140684836674,
    "owner": 5654140684002895
  }
}

```

--- 

### <a name="section-getmembers"></a>Get Organisation Members [GET: /members]

This endpoint retrieves a list of members associated with the organisation linked to the provided API key.

- **URL:** `/members`
- **Method:** `GET`
- **Authentication:** Required (API Key)

#### Request Body

None

#### Response

Returns a list of members (users with extended profile information) in JSON format.

```json
{
  "status": 200,
  "data": [
    {
      "consents": {
        "EMAIL": true,
        "SMS": false,
        "WHATSAPP": true
      },
      "email": "andy@company.com",
      "name": "Andy Zane",
      "org_id": 5648364728190547,
      "phone_number": "+447772572105",
      "preferences": [
        "MINOR",
        "MAJOR",
        "CRITICAL"
      ],
      "role": "Founder"
    },
    {
      "consents": {
        "EMAIL": false,
        "SMS": false,
        "WHATSAPP": true
      },
      "email": "becky@company.com",
      "name": "Becky Yates",
      "org_id": 5648364728190547,
      "phone_number": "+447772572105",
      "preferences": [
        "MAJOR",
        "CRITICAL"
      ],
      "role": "Managing Director"
    },
    {
      "consents": {
        "EMAIL": true,
        "SMS": true,
        "WHATSAPP": true
      },
      "email": "charlie@company.com",
      "name": "Charlie Xavier",
      "org_id": 5648364728190547,
      "phone_number": "+447772572105",
      "preferences": [
        "CRITICAL"
      ],
      "role": "Head of Marketing"
    }
  ]
}

```

--- 

### <a name="section-postescalate"></a>Escalate [POST: /escalate]

The /escalate endpoint is used to report critical issues or bugs. This endpoint attempts to send a notification via
WhatsApp first, and if it fails, it sends an email notification.

- **URL:** `/escalate`
- **Method:** `POST`
- **Authentication:** Required (API Key)

#### Request Body

| Param         | Type     | Req     | Description                                                                                  | Guidance                                                                                                                     | 
|---------------|----------|---------|----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `code`        | `string` | `true`  | The error code of the issue. This is defined on your side and should be unique to the issue. | It is used for summarising such has subsequent escalations with the same code are thought to refer to the same trigger       |
| `application` | `string` | `true`  | The name of the application where the issue occurred                                         | Defined as per your setup & is used for grouping and sorting. Format-wise, snake_case is a good idea                         |
| `message`     | `string` | `true`  | A concise message describing the issue                                                       | This is used an a quick way of identifying more about the issue and can include dynamic information about the specific issue |
| `severity`    | `string` | `true`  | The severity level of the issue                                                              | One of: `"LOW"`, `"MINOR"`, `"MAJOR"`, `"CRITICAL"`                                                                          |
| `user_ref`    | `string` | `false` | A unique reference to the user the escalation originates from (e.g. a user id).              | Do not include sensitive information as this is used in summary reports. A user_id internal to you is a good idea.           |
| `trace`       | `string` | `false` | The trace details for debugging.                                                             | Max length is 1024; will chop from start.                                                                                    |

Example body:

```json
{
  "code": "500",
  "application": "ExampleApp",
  "message": "Critical bug detected",
  "severity": "MAJOR",
  "user_ref": "5698284710984738",
  "trace": "Trace details here"
}

```

#### Response

##### 201 OK

An array of jobs with their individual data and responses. Note that the `result` object within the job object is the
same as a general response (both success and failure)

```json
{
  "status": 201,
  "data": [
    {
      "platform": "EMAIL",
      "recipients": [
        [
          "james@company.com",
          "kate@company.com",
          "lionel@company.com"
        ]
      ],
      "result": {
        "data": {
          "message": "Sent successfully"
        },
        "status": 201
      }
    },
    {
      "platform": "WHATSAPP",
      "recipients": [
        "+447772572105"
      ],
      "result": {
        "error": {
          "code": "SENDER_BLOCKED",
          "message": "The whatsapp receiver has blocked the sender. Falling back to SMS",
          "details": ""
        },
        "status": 403
      }
    },
    {
      "platform": "SMS",
      "recipients": [
        "+447772572105"
      ],
      "result": {
        "data": {
          "message": "Sent successfully"
        },
        "status": 201
      }
    }
  ]
}
```

--- 

### <a name="section-getloginfo"></a>Log information [GET: /log/<log_code>]

This endpoint retrieves the details of a specific log using its unique key and displays the information in an HTML
template.

- **URL:** `/escalate`
- **Method:** `POST`
- **Authentication:** None. This is as the link is offered as a "see more" feature

#### Request Body

**URL Parameters:**

`log_key`: The unique key identifying the log to be retrieved.

#### Response

##### 200 OK

Returns the details of the log in an HTML template.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Log Details</title>
</head>
<body>
<h1>Log Details</h1>
...
</body>
</html>
```

--- 

## <a name="section-errorcodes"></a>Error codes

A list of all noted error codes, accessible via error responses: `response["error"]["code"]`

- Authentication & access
    - `AUTH_INVALID`: occurs when the authorisation method / token is not valid
    - `AUTH_REVOKED`: occurs when the authorisation method / token is no longer valid as it was revoked. Speak to us if
      you think this is incorrect
    - `PERMISSION_DENIED`: occurs occurs when the authorisation method / token is ok but you lack the req. permission to
      take this action. Speak to us if you think this is incorrect
- Request body
    - `FIELD_MISSING`: occurs when the request body is missing one or more fields that are required
    - `FIELD_INVALID`: occurs when the request body is invalid due to one or more fields
- Data
    - `DATA_MISSING`: occurs when we are attempting to retrieve a required field from the DB but it is missing
    - `INTERNAL_DB_ERROR`: occurs when there is an issue connecting to or otherwise interacting with the DB when we
      think
      we should be able to
- General
    - `INTERNAL_SERVER_ERROR`: occurs when something unexpected does; we endeavour to minimise use of this so if it
      occurs, please reach out because it is probably something we thought could never happen

