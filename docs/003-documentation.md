
# Introduction 

This page is your guided setup: authenticate, call an endpoint, and handle errors confidently.

## The flow at a glance


flowchart TD
    A[Start] --> B[Get client_id & client_secret]
    B --> C[Redirect user to /authorize with scopes]
    C --> D[User consents]
    D --> E[Receive authorization code on your redirect_uri]
    E --> F[Exchange code for tokens at /token]
    F --> G[Store access_token (and refresh_token) securely]
    G --> H[Call API with Authorization: Bearer <access_token>]
    H --> I{HTTP 200?}
    I -- yes --> J[Parse response JSON]
    I -- no --> K{HTTP 400 clarence_asleep?}
    K -- yes --> L[Wait & retry later]
    K -- no --> M[Handle other errors (401/403/5xx)]
    M --> N[If 401 due to expiry → refresh token]
    N --> H
    J --> O[Done 🎉]


<!-- theme: info -->
> #### Important Essentials For Success
>
> **Base URL:** `https://external.pleo.io/v3`\
> **Version:** `v3`\
> **Auth:** OAuth 2.0 → Bearer token (see **Authentication**)\
> **Endpoints:** `/clarence/sit` (POST), `/clarence/paw` (GET), `/clarence/heel` (POST)