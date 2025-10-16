# Clarence Services API

Meet Clarence, Pleos most popular colleague. This API lets you ask Clarence to:
-  **Sit**
- Offer a **paw**
- **heel** 

With simple HTTP calls.

## What you will find here

There is a choice of two paths to take:
  1. If you are experienced with Pleo Services utilise the [Quick Start](#quick-start-3-minutes) 
  2. If you are new to the servies, go to the [Documentation](./003-documentation.md) section for a detailed walkthrough on how to get started.

### 1. **Documenation (Start Here)** : A step-by-step guide that shows you how to:
   - Authenticate with **OAuth 2.0**
   - Store and use your **Bearer token**
   - Call your first endpoint successfully
   - Handle **errors** (e.g., `clarence_asleep`)
### 2. **Authentication** : header formats, token lifetime, basic security tips.
### 3. **Endpoints** : parameters, examples, and responses for `sit`, `paw`, and `heel`.
### 4. **Errors** : consistent JSON error shapes and how to fix them quickly.

---

## Quick Start (3 minutes)

Get up and running with the Clarence API in three simple steps.

    Please follow the main guide if this your first time using this service 

### Download the OpenAPI spec

Open this documentation in Stoplight and click the `Download` button at the top right of the API Reference panel.
You can then:

- Import it directly into Postman or Insomnia, or

- Explore it locally in your favourite OpenAPI viewer.

💡 This gives you all endpoints, parameters, examples, and schema definitions for Clarence’s API.

---

### Acquire an access token (OAuth 2.0 → Bearer)

Use your `client_id` and `client_secret` to request a token from the authorization server.

```bash
curl -X POST "https://auth.example.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=<CLIENT_ID>" \
  -d "client_secret=<CLIENT_SECRET>"
```

Save the `access_token` from the response.

> You’ll use this token in the Authorization header for all API calls.

--- 

### Make your first call

Ask Clarence for a paw:

```bash
curl -X GET "https://external.pleo.io/v3/clarence/paw?side=left" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json"
```

200 OK

```json
{ 
    "status": "ok", 
    "action": "paw", 
    "detail": "Clarence offers his left paw." 
}
```

400 Bad Request

```json
{ 
    "type": "clarence_asleep",
    "message": "Clarence is asleep. Try again in a few minutes or check his nap schedule." }
```

> Next → see Help for the full OAuth flow (authorization, refresh, and error handling).