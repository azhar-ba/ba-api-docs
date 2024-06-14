# Bright Artistry API Docs

## Table of Contents

1. General Information
2. Authentication
3. The Escalate Endpoint

## General Information

The Bright Artistry API is designed to allow clients to report critical issues and receive timely notifications through
multiple channels. The API ensures secure and efficient communication, enabling clients to escalate issues and track
their resolutions.

- **Base URL**:
    - Production: `https://api.brightartistry.com`
    - Development: `https://api-dev.brightartistry.com`
- **Request Format**: All endpoints accept a JSON request body unless specified otherwise.
- **Response Format**: All responses, whether errors or successes, follow a consistent structure for easy parsing and
  handling.

## Authentication

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

- `status`: HTTP status code indicating the success of the operation (typically 201 for resource creation).
- `error`: Contains detailed information about the operation. This contains
    1. `code`: A string enum. You can see a list of these in the next section
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

## Endpoints

### GET: /profile

The /profile endpoint is used to retrieve information about the authenticated client, including their primary contact,
critical contacts, other contacts, and the assigned Bright Artistry representative (ba_rep).

- URL: /profile
- Method: GET
- Authentication: Required (API Key)

#### Request Body

None

#### Response

```json
{
  "status": 200,
  "data": {
    "ba_rep": "tester@brightartistry.com",
    "critical_contacts": [
      {
        "email": "jane.smith@company.com",
        "name": "Jane Smith",
        "phone_number": null,
        "role": "Developer"
      },
      {
        "email": "bob.johnson@company.com",
        "name": "Bob Johnson",
        "phone_number": "098-765-4321",
        "role": "CTO"
      }
    ],
    "name": "Example Client",
    "other_contacts": [
      {
        "email": "alice.brown@company.com",
        "name": "Alice Brown",
        "phone_number": null,
        "role": "Support"
      },
      {
        "email": "tom.white@company.com",
        "name": "Tom White",
        "phone_number": null,
        "role": "QA"
      }
    ],
    "primary_contact": {
      "email": "john.doe@company.com",
      "name": "John Doe",
      "phone_number": "123-456-7890",
      "role": "Founder"
    }
  }
}

```

### POST: /escalate

The /escalate endpoint is used to report critical issues or bugs. This endpoint attempts to send a notification via
WhatsApp first, and if it fails, it sends an email notification.
URL: `/escalate`
Method: `POST`
Authentication: Required (API Key)

#### Request Body

| Param         | Type     | Req     | Description                                                                                               | 
|---------------|----------|---------|-----------------------------------------------------------------------------------------------------------|
| `code`        | `string` | `true`  | The error code of the issue. This is set on your side and can be a HTTP code or internal application code |
| `application` | `string` | `true`  | The name of the application where the issue occurred                                                      |
| `message`     | `string` | `true`  | A detailed message describing the issue                                                                   |
| `severity`    | `string` | `true`  | The severity level of the issue (LOW, MINOR, MAJOR, CRITICAL)                                             |
| `trace`       | `string` | `false` | The trace details for debugging                                                                           |

Example body:

```json
{
  "code": "500",
  "application": "ExampleApp",
  "message": "Critical bug detected",
  "severity": "HIGH",
  "trace": "Trace details here"
}

```

#### Response

##### 201 OK

```json
{
  "status": 201,
  "data": {
    "message": "Escalation actions completed successfully"
  }
}
```

```json
{
  "status": 201,
  "data": {
    "message": "Escalation actions completed successfully. Whatsapp's failed so fell back to email"
  }
}
```

##### 400 Bad Request

```json
{
  "status": 400,
  "error": {
    "message": "Bad Request",
    "details": "The 'severity' field is required."
  }
}
```

## Error codes

A list of all noted error codes, accessible via error responses: `response["error"]["code"]`

- Authentication & access
  - `AUTH_INVALID` 
  - `AUTH_REVOKED`    
  - `PERMISSION_DENIED`
- Request body 
  - `FIELD_MISSING`
  - `FIELD_INVALID`
- Data 
  - `DATA_MISSING`

