
# Intergration Guide 

This page is your guided setup: authenticate, call an endpoint, and handle errors confidently.

## The flow at a glance

## Authentication Flow

graph TB
    c1-->a2
    subgraph ide1 [one]
    a1-->a2
    end

## Use token to call available endpoints
graph TB
    H[Authenticated: have access_token] --> I[Choose endpoint: sit, paw, or heel]
    I --> J[Call API with header Authorization: Bearer ACCESS_TOKEN]
    J --> K{Response 200?}
    K -- Yes --> L[Parse JSON success payload]
    K -- No --> M{Was it 400 clarence_asleep?}
    M -- Yes --> N[Wait and retry later]
    M -- No --> O{Other errors 401 403 429 5xx}
    O --> P[If 401 due to expiry, refresh token]
    P --> J
    L --> Q[Done]



<!-- theme: info -->
> #### Important Essentials For Success
>
> **Base URL:** `https://external.pleo.io/v3`\
> **Version:** `v3`\
> **Auth:** OAuth 2.0 → Bearer token (see **Authentication**)\
> **Endpoints:** `/clarence/sit` (POST), `/clarence/paw` (GET), `/clarence/heel` (POST)