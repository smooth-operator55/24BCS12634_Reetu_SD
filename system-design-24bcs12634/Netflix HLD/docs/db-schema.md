# Database Design and Schema Modeling

A major rule of modern system design is the **"Database per Service" pattern**. We never connect all microservices to one single database. If one database gets overloaded or crashes, the entire platform goes dark. By separating them, a crash in the recommendation service will not stop users from logging in or playing videos.

Here is how our distinct storage tiers are configured:

### 1. User & Subscription Database (Relational - MySQL / PostgreSQL)
We use a relational database here because account emails, passwords, and credit card payments require strict transactional consistency (ACID compliance). We cannot afford data errors with financial or login information.

#### Table: `users`
Tracks the primary owner of the subscription account.
* `user_id` (VARCHAR 36, Primary Key): A unique digital identifier generated as a UUID.
* `email` (VARCHAR 255, Unique, Indexed): Used for login validation; cannot be duplicated in the system.
* `password_hash` (VARCHAR 255): Salted and hashed password string; plain text passwords are never stored.
* `subscription_tier` (VARCHAR 50): Restricts account access level (e.g., "Basic", "Standard", "Premium").
* `is_active` (BOOLEAN): A quick flag used by the playback gateway to check if the monthly bill was paid.
* `created_at` (TIMESTAMP): Logs when the user first joined the platform.

#### Table: `user_profiles`
Handles the sub-profiles attached to a main user account.
* `profile_id` (VARCHAR 36, Primary Key): Unique identifier for this specific profile.
* `user_id` (VARCHAR 36, Foreign Key): Links this profile back to the master `users` table.
* `profile_name` (VARCHAR 50): The display name chosen by the user (e.g., "Kids Room").
* `maturity_rating_limit` (VARCHAR 10): Enforces parental locks (e.g., "PG-13", "R").

---

### 2. Content Metadata Catalog (NoSQL Document Store - MongoDB / Cassandra)
Movie descriptions, actor lists, subtitle links, and thumbnail images are highly "read-heavy" data assets. They rarely change but are viewed billions of times a day. A NoSQL document database stores this data as flexible JSON-like objects, allowing the platform homepage to load instantly.

#### Catalog Collection Schema Example:
```json
{
  "video_id": "movie_uuid_77a11b22",
  "title": "The Golden Architecture Code",
  "synopsis": "A group of junior engineers discover a server framework that never crashes.",
  "release_date": "2026-09-16",
  "maturity_rating": "PG-13",
  "duration_seconds": 7200,
  "genres": ["Tech Thriller", "Suspense"],
  "cast": ["Alice Dev", "Bob Ops", "Charlie Code"],
  "video_assets": {
    "manifest_file_url": "https://netflix-adjacent.net",
    "poster_image_url": "https://netflix-adjacent.net",
    "preview_trailer_url": "https://netflix-adjacent.net"
  }
}
```
