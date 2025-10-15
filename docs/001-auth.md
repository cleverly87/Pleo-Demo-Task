# Authentication (OAuth 2.0)

Clarence’s API uses **OAuth 2.0**. You’ll call endpoints with an **access token** in the `Authorization` header.

## Choose a flow

- **Authorization Code (recommended)** – when a user grants consent to your app.
- **Client Credentials** – machine-to-machine (no user), server-side only.

All endpoints require the scope: **`clarence.actions`**.

---

## Authorization Code flow (user consent)

**1) Redirect the user to authorize**

```http
GET https://auth.example.com/authorize?response_type=code\
&client_id=YOUR_CLIENT_I\
&redirect_uri=https://yourapp.com/callback\
&scope=clarence.actions\
&state=RANDOM_STRING
```

- **response_type=code** : asks for an authorization code (the standard for server/web apps).

- **client_id** : your app’s public identifier (issued in the developer portal).

- **redirect_uri** : where we’ll send the user back after consent; must match a value you registered.

- **scope** : the permissions you’re asking for (here: clarence.actions to call sit/paw/heel).

- **state** : a random string you generate to prevent CSRF; you must verify it on return.

**2) Handle the callback**

After consent we redirect to (what your app receives):

```http
https://yourapp.com/callback?code=AUTH_CODE&state=RANDOM_STRING
```

- **code** : short-lived one-time code you’ll swap for tokens.

- **state** : echo of the value you sent; verify it matches before proceeding.

**3) Exchange the code for tokens**

```bash
curl -X POST "https://auth.example.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code" \
  -d "code=<AUTH_CODE>" \
  -d "redirect_uri=https://yourapp.com/callback" \
  -d "client_id=<CLIENT_ID>" \
  -d "client_secret=<CLIENT_SECRET>"
```

### Request fields

- **grant_type=authorization_code**: Tells the server which OAuth grant you’re using.

- **code** : The one from the callback.

- **redirect_uri**  Must be identical to the one used at /authorize.

- **client_id / client_secret** : Identify and authenticate your app at the token endpoint.

### Response

```json
{
  "access_token": "eyJhbGciOi...",
  "refresh_token": "r1Xf9...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

- **access_token** : What you send to the API in Authorization: Bearer

- **refresh_token** : Lets you get a new access token without asking the user again.

- **token_type** : Always Bearer here.

- **expires_in** : Lifetime (seconds) of the access token.

- **scope** : Scopes actually granted (may be fewer than requested).

**4) Call the API with the access token**

```bash
curl -X POST "https://external.pleo.io/v3/clarence/sit" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json"
```

- **Authorization**: Bearer — standard header for OAuth 2.0 protected APIs.

- Expect 200 on success; handle 400 (clarence_asleep) per the Errors page.

---

## Client Credentials flow (server to server)

```bash
curl -X POST "https://auth.example.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=<CLIENT_ID>" \
  -d "client_secret=<CLIENT_SECRET>" \
  -d "scope=clarence.actions"
```

Use the returned access_token in the Authorization header.

## Refresh the access token (when it expires)

When the access token expires (e.g., a 401 response):

```bash
curl -X POST "https://auth.example.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=refresh_token" \
  -d "refresh_token=<YOUR_REFRESH_TOKEN>" \
  -d "client_id=<CLIENT_ID>" \
  -d "client_secret=<CLIENT_SECRET>"
  ```

  - **grant_type=refresh_token** : Uses your refresh token to mint a fresh access token.

  - Rotate and store any **new** refresh token if the server returns one.

  Store the new token(s) securely and retry your request.   

Required header

```http
Authorization: Bearer <ACCESS_TOKEN>
```

Always use HTTPS and keep tokens on the server side. For front-end apps, complete the OAuth flow on a secure backend.

---

### Common OAuth and API Errors

| Where | Error | What it means | How to fix |
|-------|--------|----------------|------------|
| `/authorize` | `invalid_redirect_uri` | The redirect_uri does not match a registered value. | Use an exact registered URL (including scheme and path). |
| `/authorize` | `invalid_scope` | The requested scope is not allowed for this client. | Request or enable the correct scope (e.g. `clarence.actions`). |
| `/token` | `invalid_grant` | The authorization code is missing, expired, or already used. | Restart the OAuth flow and exchange a fresh code. |
| `/token` | `invalid_client` | The client_id or client_secret is invalid. | Check credentials; rotate your secret if needed. |
| `/token` | `unsupported_grant_type` | The grant_type parameter is invalid or missing. | Ensure you are sending the correct grant_type (e.g. `authorization_code` or `refresh_token`). |
| API call | `401 unauthorized` | The access token is missing, expired, or invalid. | Acquire or refresh the token and retry with `Authorization: Bearer <ACCESS_TOKEN>`. |
| API call | `403 forbidden` | The token lacks the required scope for this endpoint. | Re-authorize and request the correct scopes. |
| API call | `429 rate_limited` | Too many requests in a short time window. | Slow down and retry with exponential backoff. |
| API call | `5xx server_error` | Temporary problem on the server. | Retry with backoff; contact support if persistent. |



