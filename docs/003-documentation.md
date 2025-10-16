
# Intergration Guide 

This page is your guided setup: authenticate, call an endpoint, and handle errors confidently.

## The flow at a glance

graph TB
  %% Authentication subgraph
  subgraph AUTH[Authentication]
    A[Start] --> B[Get client_id and client_secret]
    B --> C[Redirect user to /authorize with scope clarence.actions]
    C --> D[User consents]
    D --> E[Receive authorization code at redirect_uri]
    E --> F[Exchange code for tokens at /token]
    F --> G[Store access_token and refresh_token securely]
  end

  %% Choose endpoint fan-out
  G --> H[Choose endpoint]
  H --> S1["POST /clarence/sit"]
  H --> S2["GET /clarence/paw (side=left or right)"]
  H --> S3["POST /clarence/heel"]

  %% Single call node
  S1 --> I[Call API with Authorization: Bearer ACCESS_TOKEN]
  S2 --> I
  S3 --> I

  %% Response handling
  I --> J{Response}
  J -->|"200 OK"| K[Parse success JSON and proceed]
  J -->|"400 clarence_asleep"| L[Wait and retry later]
  J -->|"Other 4xx or 5xx"| M[Handle other errors such as 401, 403, 429, or 5xx]

  %% Optional loop for refresh
  M --> N{Was it 401 unauthorized?}
  N -->|"Yes"| O[Refresh token using refresh_token grant]
  O --> I
  N -->|"No"| P[Fix request or retry after backoff]
  P --> I



<!-- theme: info -->
> #### Important Essentials For Success
>
> **Base URL:** `https://external.pleo.io/v3`\
> **Version:** `v3`\
> **Auth:** OAuth 2.0 → Bearer token (see **Authentication**)\
> **Endpoints:** `/clarence/sit` (POST), `/clarence/paw` (GET), `/clarence/heel` (POST)