# CardDAV Emulator MVP

## Goal
A secure CardDAV emulator written in Go with PostgreSQL providing a web interface and CardDAV endpoints.

## Features
### Web Interface
- Add, edit, and delete contacts
- Manage address books and principals
- Assign contacts to multiple address books
- Restrict contact fields per principal or group
- Admins can assign principals to address books and unlock accounts

### CardDAV Support
- Strict vCard 3.0 with Apple `X-*` extensions
- Fields: name, pronunciation, title, company, notes
- Unlimited phone numbers, emails, URLs, and addresses with custom labels
- One-way sync from server to client

### Authentication
- CardDAV clients use Basic Auth
- Web interface uses username/password login
- Temporary API keys after login (IP bound, expire after inactivity)

### Security & Encryption
- HTTPS only
- Login throttling: incremental delay on failure, 1 hour lock after 5 failures, admin unlock on repeated failures
- Account-specific Data Encryption Key (DEK) encrypted with a KEK in AWS Secrets Manager
- Row Field Encryption Keys (FEKs) derived from the account DEK
- All PII encrypted or hashed using AEAD with 12-byte nonce and account ID as associated data
- Database user runs only predetermined functions and adheres to row-level security
- PostgreSQL encrypted at rest; database never has access to application keys
- Passwords hashed with Argon2id and per-user salt

## Architecture
- Single Go server using `net/http`
- Single PostgreSQL instance on AWS RDS
- Application on AWS EC2
- Object-oriented style for future extensibility (e.g., caching)
- Suggested layout: `cmd/` for main, `internal/` for business logic, `db/` for migrations

## Out of Scope for MVP
- Client-to-server synchronization
- Cache layer
- Multi-region deployment
- External integrations

## Future Considerations
- Expanded vCard fields
- Caching layer
- Audit logging
- Multi-user scaling
- Improved admin UI
