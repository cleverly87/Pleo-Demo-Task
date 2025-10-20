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
See [Authentication](#002-auth.md) for full details on obtaining and refreshing tokens.

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
| **Documentation (Start Here)** | **A guided walkthrough for new users**: From setup to first successful request, including authentication, example calls, and troubleshooting tips. |
| **Authentication** | **Detailed guide to OAuth 2.0**: Learn how to obtain, use, and refresh access tokens securely. |
| **API Reference** | **The full technical breakdown**: Endpoints for `sit`, `paw`, and `heel`, request and response structures, examples, and error definitions. |


---

## Why it matters

Clarence’s API isn’t just fun — it reflects our philosophy at Pleo:  
make complex things simple, make simple things delightful, and make sure the documentation feels like a friendly teammate guiding you through it.


---

## Quick Start (3 minutes)

Get up and running with the Clarence API in a couple of simple steps.

<!-- theme: info -->

> #### New Users
> Please follow the main guide if this your first time using this service [HERE](003-documentation.md)

> 🔐 **Note:** This guide assumes basic familiarity with **OAuth 2.0** and access tokens.  
> If you’re new to OAuth, you can find a detailed walkthrough in the [Authentication guide](./002-auth.md).



### Option 1 : Try it in your browser (mock server)

You can experiment with Clarence’s endpoints directly from this documentation.

1. Open the [**API Documentation**](../openapi/openapi.yaml) page.  
2. Select any endpoint under the **Clarence Actions** tag (`/clarence/sit`, `/clarence/paw`, or `/clarence/heel`).  
3. Click **Send API Request** and choose the **Mock Server** environment.  
4. Send a request to see realistic sample responses:
5. Change the response by selecting **Mock Settings** and then the response type.

| Response | Meaning |
|-----------|----------|
| **200 OK** | Clarence successfully performs the action (sit, paw, heel). |
| **4 xx** | The request is invalid or Clarence is asleep — you’ll receive a friendly JSON error explaining why. |

> 💡 The mock server is perfect for exploring request and response formats without needing real credentials.

---

## Option 2 : Use it locally (Postman, Bruno, or similar)

Prefer to work in your own API-testing setup?  

Download the complete OpenAPI schema and import it into your tool of choice.

1. Go to the **[API Documentation page](./openapi/openapi.yaml)**.  
2. In the top-right corner, click **Export → Bundled References**.  
3. Your browser will download a single `.yaml` file containing the entire schema.  
4. Import that file into **Postman**, **Bruno**, or any OpenAPI-compatible client.  

Once imported, you can:
- Inspect all endpoints, parameters, and responses locally.  
- Attach your own **OAuth 2.0 access token** in the `Authorisation: Bearer <TOKEN>` header.  
- Run live calls against the API or your mock server.

> 🔐 Remember: production requests require a valid OAuth token.  
> See [Authentication](./002-auth.md) for detailed steps on obtaining and refreshing tokens.

---