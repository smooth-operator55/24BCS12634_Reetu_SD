# Application API Endpoints Specification

API endpoints act as the communication contracts between our front-end client apps (TVs, phones) and the backend microservices. All incoming client traffic passes through a secure Load Balancer and API Gateway tier to protect the platform.

### 1. User Authentication (Login Engine)
* **HTTP Method:** `POST`
* **Route Path:** `/api/v1/auth/login`
* **Description:** Validates user credentials. If accurate, it hands back a secure digital passport called a JWT (JSON Web Token). The client app attaches this token to all future requests to prove who they are.

#### Request Payload (JSON Format):
```json
{
  "email": "student@university.edu",
  "password": "myhighlysecurepassword123"
}
```

#### Success Response Structure (200 OK):
```json
{
  "status": "Authentication Successful",
  "session_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.user_data_encrypted_string",
  "expires_in_seconds": 86400,
  "user_profile_defaults": {
    "account_id": "user_uuid_112233",
    "tier": "Premium"
  }
}
```

---

### 2. Video Playback Authorization (The Gatekeeper Check)
* **HTTP Method:** `GET`
* **Route Path:** `/api/v1/videos/:video_id/play`
* **Description:** When a user clicks the "Play" triangle icon on a movie card, this endpoint triggers. It checks if the user's account is active and paid up. If valid, it generates a time-sensitive security token that unlocks the video stream from our Content Delivery Network (CDN) servers.
* **Required Security Header:** `Authorization: Bearer <session_token>`

#### Success Response Structure (200 OK):
```json
{
  "authorization_status": "Access Granted",
  "video_id": "movie_uuid_77a11b22",
  "cdn_security_token": "token_auth_hash_string_valid_for_3_hours",
  "streaming_details": {
    "streaming_protocol": "HLS (HTTP Live Streaming)",
    "master_manifest_url": "https://platform.net"
  }
}
```
