# Authentication (OAuth 2.0)

Clarence’s API uses **OAuth 2.0**. You’ll call endpoints with an **access token** in the `Authorisation` header.

Tokens from Pleo are **opaque** (do not decode).  

- Access-token lifetime is returned as `expires_in`. 
- Refresh tokens are valid **for at least 60 days**; using an **expired** refresh token invalidates all active refresh tokens for security.

---

## Quick setup (Authorisation Code flow)

Follow these steps to obtain your first access token and call an endpoint.

## Prerequisites

Before starting the OAuth flow, make sure you’ve completed the following setup steps.

1. **Register your application**  
   Create an OAuth client in the [Pleo Developer Portal](https://developers.pleo.io/docs/oauth-client-registration)  
   *(or use [this sample registration form](#))* to obtain your:
   - `client_id`
   - `client_secret`
   - registered `redirect_uri`

   > These credentials identify your app when requesting tokens.

2. **Confirm redirect URI**  
  -  Ensure your `redirect_uri` exactly matches what you’ll use during the authorisation flow (including protocol and path, e.g., `https://yourapp.com/callback`).

3. **Know your required scope**  

   All Clarence endpoints require the scope:  
   
   ```text
   clarence.actions
   ```

Request this scope when generating tokens.

Once these steps are complete, you’re ready to begin the OAuth flow.

---

## Authorisation Code flow

### 1) Redirect the user to authorise
Send your user to Pleo’s authorisation page:

```http
GET https://auth.pleo.io/oauth/authorise?response_type=code\
&client_id=YOUR_CLIENT_ID\
&redirect_uri=https://yourapp.com/callback\
&scope=clarence.actions\
&state=RANDOM_STRING
```

- Redirect_uri **MUST** exactly match a URL you control.

- State is any random string you’ll verify later.

### 2) Handle the callback

After consent, Pleo redirects to your app with a short-lived code:

```http
https://yourapp.com/callback?code=AUTH_CODE&state=RANDOM_STRING
```

Verify **state** matches what you sent.

### 3) Exchange the code for tokens

Swap the code for an access token (and refresh token):

```bash
curl -X POST "https://auth.pleo.io/oauth/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorisation_code" \
  -d "code=<AUTH_CODE>" \
  -d "redirect_uri=https://yourapp.com/callback" \
  -d "client_id=<CLIENT_ID>" \
  -d "client_secret=<CLIENT_SECRET>"
```

#### Example response

```json
{
  "access_token": "3b4c78c0-12de-44c8-b5c3-1b9e21b38af9",
  "refresh_token": "0df6e8f9-8b4f-4b8a-93c7-27ccfab4fca7",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "clarence.actions"
}
```

---

### Using your tokens

Once the `access_token` has been issued, include it in the `Authorisation` header for every request to the Clarence API:

```http
Authorisation: Bearer <ACCESS_TOKEN>
```
- Access tokens expire after the duration specified in the **expires_in** field (typically 3600 seconds).

When this happens, use your **refresh_token** to request a new access token by sending a** refresh_token** grant.

💡 When a refresh token is used successfully, a new refresh token is also issued.

You should replace the old one with the new value to stay in sync and prevent invalidation issues.

<!-- theme: warning -->
> #### Always
>
> Keep your tokens secure — never expose them in client-side code or version control.
>
>Use HTTPS for all token and API requests.
>
>Re-authorise from scratch if a refresh request fails, as expired refresh tokens invalidate all others for that app.