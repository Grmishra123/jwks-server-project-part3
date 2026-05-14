# 🔐 JWT Authentication Server

A secure, production-ready **JWT authentication server** built with FastAPI and Python — featuring RSA key encryption, automatic key rotation, JWKS support, and per-IP rate limiting.

---

## ✨ Features

| Feature | Details |
|---|---|
| 🔑 User Registration | Passwords hashed with **Argon2** (winner of Password Hashing Competition) |
| 🛡️ JWT Authentication | Signed with **RSA keys** encrypted at rest via **AES-GCM** |
| 🔄 Key Rotation | Automatic RSA key generation and expiry management |
| 🌐 JWKS Endpoint | Exposes active public keys at `/.well-known/jwks.json` |
| 🚦 Rate Limiting | Per-IP throttling — max 1 auth attempt per 5 seconds |
| 📋 Auth Logging | Every authentication logged with timestamp and IP address |
| 🧹 Key Cleanup | Utility to purge expired keys from the database |

---

## 🛠️ Tech Stack

- **Framework:** FastAPI + Uvicorn
- **Database:** SQLite3
- **Cryptography:** RSA (JWT signing), AES-GCM (key encryption), Argon2 (password hashing)
- **Testing:** pytest + pytest-cov

---

## ⚙️ Installation

### Prerequisites
- Python 3.8+
- SQLite3 (bundled with Python)

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/Grmishra123/jwks-server-project-part3.git
cd jwks-server-project-part3

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install fastapi uvicorn python-dotenv pydantic jose cryptography argon2-cffi

# 4. (Optional) Install testing dependencies
pip install pytest pytest-cov
```

---

## 🔧 Configuration

Create a `.env` file in the project root:

```env
NOT_MY_KEY=<your_base64_encoded_32_byte_key>
```

Generate a secure key with:

```python
import os, base64
print(base64.b64encode(os.urandom(32)).decode())
```

---

## 🗄️ Database

On startup, the server automatically initializes `totally_not_my_privateKeys.db` with three tables:

- **`keys`** — encrypted RSA private keys, nonces, and expiry timestamps
- **`users`** — user credentials and metadata
- **`auth_logs`** — authentication history by IP and timestamp

> A default admin user is seeded on first run (`admin` / `password`).

---

## 🚀 Running the Server

```bash
uvicorn jwt_server:app --reload --host 0.0.0.0 --port 8080
```

Server will be available at `http://localhost:8080`

---

## 📡 API Endpoints

### `POST /register`
Register a new user. Returns a generated password.

```json
// Request
{ "username": "your_username", "email": "optional@example.com" }

// Response
{ "password": "<generated_password>" }
```

---

### `POST /auth`
Authenticate and receive a signed JWT.

| Query Param | Type | Description |
|---|---|---|
| `expired` | bool (optional) | If `true`, issues an already-expired token (for testing) |

```json
// Request
{ "username": "your_username", "password": "your_password" }

// Response — 200 OK
{ "jwt": "<signed_jwt_token>" }

// Response — 401 Unauthorized
// Response — 429 Too Many Requests (rate limit exceeded)
```

---

### `GET /.well-known/jwks.json`
Returns the JSON Web Key Set of all active public keys.

```json
{
  "keys": [
    {
      "kty": "RSA",
      "kid": "<key_id>",
      "alg": "RS256",
      "use": "sig",
      "n": "<modulus>",
      "e": "AQAB"
    }
  ]
}
```

---

## 🧪 Testing

```bash
pytest test_jwt_server.py
```

Test coverage includes:
- ✅ User registration
- ✅ Authentication success & failure
- ✅ JWKS endpoint content
- ✅ Expired token handling
- ✅ Expired key cleanup
- ✅ HTTP method validation
- ✅ Rate limiting
- ✅ Authentication logging

---

## 🧹 Utilities

Manually purge expired keys (useful in testing):

```python
from jwt_server import clean_up_expired_keys
clean_up_expired_keys()
```

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 👤 Author

**Grishab Mishra** — [GitHub](https://github.com/Grmishra123)
