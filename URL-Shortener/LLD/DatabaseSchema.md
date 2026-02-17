# Database Schema

## Table: urls

Columns:

- id (BIGINT, Primary Key, Auto Increment or Snowflake ID)
- short_code (VARCHAR(10), UNIQUE, Indexed)
- long_url (TEXT, NOT NULL)
- created_at (TIMESTAMP)
- expires_at (TIMESTAMP, nullable)
- is_custom (BOOLEAN)

Indexes:

1. UNIQUE INDEX on short_code
2. INDEX on created_at
3. INDEX on expires_at

Notes:
- short_code is the main lookup key.
- id used for internal reference.
