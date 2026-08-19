# Environment Variables Template

Copy this file to `.env` and fill in real values. Never commit `.env` to git.

```bash
# --- Core ---
# Database connection
DATABASE_URL=postgresql+asyncpg://user:password@localhost:5432/{app_name}
# For SQLite local development:
# DATABASE_URL=sqlite+aiosqlite:///./dev.db

# --- Auth ---
# Generate with: openssl rand -hex 32
SECRET_KEY=change-me-to-a-random-string
# JWT expiry
ACCESS_TOKEN_EXPIRE_MINUTES=30

# --- App ---
# Run mode: development, staging, production
APP_ENV=development
# Log level: DEBUG, INFO, WARNING, ERROR
LOG_LEVEL=DEBUG

# --- External Services (uncomment and fill when needed) ---
# SMTP_HOST=smtp.example.com
# SMTP_PORT=587
# SMTP_USER=your-email@example.com
# SMTP_PASSWORD=your-password
# 
# STRIPE_SECRET_KEY=sk_test_...
# STRIPE_WEBHOOK_SECRET=whsec_...
#
# AWS_ACCESS_KEY_ID=...
# AWS_SECRET_ACCESS_KEY=...
# AWS_S3_BUCKET=my-bucket
```

## Rules for AI Agents

- Never hardcode secrets. Always use `os.getenv("VARIABLE_NAME")` or `pydantic-settings` for Python
- NEVER output a real API key, token, or password in code, docs, or commit messages
- The `.env` file is listed in .gitignore — do not generate code that reads `.env` directly; use the config module in `core/config.py`