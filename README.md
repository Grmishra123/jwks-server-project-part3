# JWT Authentication Server

A FastAPI-based JWT authentication server built with Python, RSA signing, encrypted key storage, JWKS support, and rate limiting.

This project was built to explore secure authentication workflows, key management, and API security practices.

For educational and authorized testing purposes only.

## Features

- User registration with Argon2 password hashing
- JWT authentication using RSA signing
- AES-GCM encrypted private key storage
- Automatic RSA key rotation
- JWKS endpoint support
- Per-IP rate limiting
- Authentication logging
- Expired key cleanup utility

## Tech Stack

- FastAPI
- Python
- SQLite3
- RSA
- AES-GCM
- Argon2
- pytest

## Installation

Clone the repository:

```bash
git clone https://github.com/Grmishra123/jwks-server-project-part3.git
cd jwks-server-project-part3
```

Create and activate a virtual environment:

```bash
python -m venv venv

# macOS/Linux
source venv/bin/activate

# Windows
venv\Scripts\activate
```

Install dependencies:

```bash
pip install fastapi uvicorn python-dotenv pydantic jose cryptography argon2-cffi
```

Optional testing dependencies:

```bash
pip install pytest pytest-cov
```

## Configuration

Create a `.env` file in the project root:

```env
NOT_MY_KEY=<base64_encoded_32_byte_key>
```

Generate a secure key:

```python
import os
import base64

print(base64.b64encode(os.urandom(32)).decode())
```

## Database

The server initializes the SQLite database automatically on startup.

Tables include:

- `keys`
- `users`
- `auth_logs`

The database stores encrypted RSA keys, user credentials, and authentication logs.

## Running the Server

```bash
uvicorn jwt_server:app --reload --host 0.0.0.0 --port 8080
```

Server runs at:

```text
http://localhost:8080
```

## API Endpoints

### Register User

```http
POST /register
```

Request:

```json
{
  "username": "testuser",
  "email": "user@example.com"
}
```

Response:

```json
{
  "password": "generated_password"
}
```

---

### Authenticate User

```http
POST /auth
```

Request:

```json
{
  "username": "testuser",
  "password": "password"
}
```

Successful response:

```json
{
  "jwt": "<signed_token>"
}
```

Supports optional expired token generation:

```text
/auth?expired=true
```

---

### JWKS Endpoint

```http
GET /.well-known/jwks.json
```

Returns active public keys:

```json
{
  "keys": [
    {
      "kty": "RSA",
      "kid": "example-key-id",
      "alg": "RS256",
      "use": "sig"
    }
  ]
}
```

## Testing

Run tests with:

```bash
pytest test_jwt_server.py
```

Current test coverage includes:

- User registration
- Authentication success and failure
- JWT validation
- JWKS endpoint responses
- Expired token handling
- Expired key cleanup
- Rate limiting
- Authentication logging

## Utilities

Clean expired RSA keys manually:

```python
from jwt_server import clean_up_expired_keys

clean_up_expired_keys()
```

## Future Improvements

- Add refresh token support
- Add role-based access control
- Add PostgreSQL support
- Improve monitoring and logging
- Add Docker deployment

## License

MIT License

## Author

Grishab Mishra

GitHub:
https://github.com/Grmishra123
