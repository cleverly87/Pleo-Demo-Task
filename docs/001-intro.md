# Clarence Services API

Meet **Clarence** – Pleo’s most popular (and occasionally napping) colleague.  
This playful API lets you interact with Clarence through simple HTTP requests — no tennis balls required.

Use the API to:
- Tell Clarence to **sit**
- Ask him to offer a **paw** (choose left or right)
- Command him to **heel** and walk obediently beside you

When Clarence is asleep, your request will return a friendly error message so you can retry later — because even good boys need naps.

---

## About this API

The Clarence Services API is a standard RESTful interface that shows how we approach developer-friendly design at Pleo: clear structure, meaningful responses, and documentation that’s as approachable as the people behind it.

Every endpoint requires **OAuth 2.0** authentication with a Bearer token.  
See [Authentication](#authentication-oauth-20) for full details on obtaining and refreshing tokens.

---

## What you’ll find here

You can explore Clarence’s API in two ways:

1. **Experienced with Pleo APIs?**  
   Jump straight to the [Quick Start](#quick-start-3-minutes) and start calling endpoints in minutes.

2. **New to Pleo or APIs in general?**  
   Head to the [Documentation](./003-documentation.md) for a detailed, step-by-step walkthrough.

### Sections overview

| Section | Purpose |
|----------|----------|
| **Documentation (Start Here)** | A guided walkthrough for new users — from setup to first successful request, including authentication, example calls, and troubleshooting tips. |
| **Authentication** | Detailed guide to OAuth 2.0 — learn how to obtain, use, and refresh access tokens securely. |
| **API Reference** | The full technical breakdown: endpoints for `sit`, `paw`, and `heel`, request and response structures, examples, and error definitions. |


---

## Why it matters

Clarence’s API isn’t just fun — it reflects our philosophy at Pleo:  
make complex things simple, make simple things delightful, and make sure the documentation feels like a friendly teammate guiding you through it.


---

## Quick Start (3 minutes)

Get up and running with the Clarence API in three simple steps.

<!-- theme: info -->

> #### New Users
> Please follow the main guide if this your first time using this service [HERE](003-documentation.md)

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