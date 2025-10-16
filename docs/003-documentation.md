
# Intergration Guide 

This page is your guided setup: 

1. Authenticate
2. Call an endpoint
3. Handle errors confidently.

## The flow at a glance

The flow below shows the full process from authentication to calling an endpoint and handling responses.  

The **first section** represents the [Authenticate Process](./002-auth.md).  
Follow that guide carefully to obtain your `access_token` before moving to the API calls.


![Flowchart](../assets/images/Flowchart.png)


<!-- theme: info -->
> #### Important Essentials For Success
>
> **Base URL:** `https://external.pleo.io/v3`\
> **Version:** `v3`\
> **Auth:** OAuth 2.0 → Bearer token (see **Authentication**)\
> **Endpoints:** `/clarence/sit` (POST), `/clarence/paw` (GET), `/clarence/heel` (POST)


---

## Step 1 — Authenticate

Before you can call any endpoint, you’ll need an OAuth 2.0 access token.

Follow the [Authenticate Process](./002-auth.md) for a detailed walkthrough.  
You’ll:

1. Register your application and obtain a `client_id` and `client_secret`.
2. Complete the Authorization Code flow.
3. Exchange the code for an `access_token`.
4. Use that token in the `Authorization` header of every request:

```http
Authorization: Bearer <ACCESS_TOKEN>
```

---

## Step 2 — Call an Endpoint

Once authenticated, you can talk to Clarence through three endpoints.

### POST /clarence/sit

> 🔍 See [API Documentation](../openapi/openapi.yaml) for full schema and example payloads.

**Description**
Instructs Clarence to sit.
This is an action command that changes his current state.

**Request**

```bash
curl -X POST "https://external.pleo.io/v3/clarence/sit" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json"
```

**Successful Response (200 OK)**

```json
{
  "status": "ok",
  "action": "sit",
  "detail": "Clarence sits obediently."
}
```

### GET /clarence/paw ?side=left | right

> 🔍 See [API Documentation](../openapi/openapi.yaml) for full schema and example payloads.

**Description**
Asks Clarence to offer a paw.
Requires a query parameter to specify which side.

**Request**

```bash
curl -X GET "https://external.pleo.io/v3/clarence/paw?side=left" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json"
```

**Successful Response (200 OK)**

```json
{
  "status": "ok",
  "action": "paw",
  "side": "left",
  "detail": "Clarence offers his left paw."
}
```

If no side is provided, the default is right.

### POST /clarence/heel

> 🔍 See [API Documentation](../openapi/openapi.yaml) for full schema and example payloads.

**Description**
Tells Clarence to heel and walk beside you.

**Request**

```bash
curl -X POST "https://external.pleo.io/v3/clarence/heel" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json"
```

**Successful Response (200 OK)**

```json
{
  "status": "ok",
  "action": "heel",
  "detail": "Clarence heels politely by your side."
}
```

---

## Step 3 — Handle Errors

Even good dogs nap. When Clarence can’t respond, you’ll receive clear, actionable errors.

**400 Bad Request – Clarence is asleep**

**Example Response**

```json
{
  "type": "clarence_asleep",
  "title": "Clarence is asleep",
  "status": 400,
  "detail": "Clarence is currently asleep. Try again in a few minutes or check his nap schedule."
}
```

**Meaning**
Your request reached Clarence, but he’s temporarily unavailable.

**How to fix**
Wait a few minutes and retry the same request. No other action is needed.

### Other Common Errors

| HTTP Code | Error | What it means | How to fix |
|------------|--------|----------------|-------------|
| **400 Bad Request** | `clarence_asleep` | Clarence received your request but is currently asleep and cannot respond. | Wait a few minutes and retry the same request. No other action is required. |
| **401 Unauthorized** | `invalid_token` | The access token is missing, expired, or invalid. | Obtain or refresh your access token, then retry. |
| **403 Forbidden** | `insufficient_scope` | The token does not include the required scope (`clarence.actions`). | Re-authorize your app with the correct scope. |
| **404 Not Found** | `invalid_endpoint` | The endpoint URL or resource does not exist. | Check that you’re using a valid path (e.g. `/clarence/paw`). |

