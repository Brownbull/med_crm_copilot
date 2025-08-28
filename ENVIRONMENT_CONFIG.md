# ENVIRONMENT_CONFIG.md
# Canonical environment variable & runtime configuration registry (machine-readable YAML section).
# Secrets are NEVER stored here—only placeholder names and descriptions.

```yaml
version: 1
environments:
  production:
    description: "Single VPS / Render instance (MVP)"
    vars:
      APP_ENV:
        value: production
        required: true
        description: "Runtime mode flag"
      DB_URL:
        value: "sqlite:///app.db"
        required: true
        description: "SQLite file path (upgrade to Postgres later)"
      SECRET_KEY:
        value: "{{GENERATE_AT_DEPLOY}}"
        required: true
        description: "Flask session & CSRF secret (rotate on compromise)"
      ATTACHMENTS_PATH:
        value: "./uploads"
        required: true
        description: "Directory for stored image attachments"
      LOG_LEVEL:
        value: "INFO"
        required: false
        description: "Logging verbosity"
      BACKUP_DIR:
        value: "./backups"
        required: true
        description: "Directory for SQLite backups (SOPS.md procedure)"
    notes:
      - "Ensure filesystem write permissions for ATTACHMENTS_PATH & BACKUP_DIR"
  development:
    description: "Local developer machine"
    vars:
      APP_ENV:
        value: development
        required: true
        description: "Enables debug tooling (avoid in production)"
      DB_URL:
        value: "sqlite:///dev.db"
        required: true
        description: "Local dev database"
      SECRET_KEY:
        value: "dev-temp-key-change"
        required: true
        description: "Non-sensitive placeholder key"
      ATTACHMENTS_PATH:
        value: "./uploads_dev"
        required: true
        description: "Local attachments path"
      LOG_LEVEL:
        value: "DEBUG"
        required: false
        description: "Verbose logging for development"
  test:
    description: "Automated / manual test runs"
    vars:
      APP_ENV:
        value: test
        required: true
        description: "Test mode flag"
      DB_URL:
        value: "sqlite:///:memory:"
        required: true
        description: "Ephemeral in-memory DB for test isolation"
      SECRET_KEY:
        value: "test-key"
        required: true
        description: "Non-secret test key"
      ATTACHMENTS_PATH:
        value: "./uploads_test"
        required: true
        description: "Temp path; can be cleaned after run"
validation:
  required_all: [APP_ENV, DB_URL, SECRET_KEY, ATTACHMENTS_PATH]
  directory_vars: [ATTACHMENTS_PATH, BACKUP_DIR]
  secret_vars: [SECRET_KEY]
rotation_policy:
  SECRET_KEY:
    trigger: ["compromise_suspected", "annual_rotation(optional)"]
    steps:
      - "Generate new 32+ char random value"
      - "Set env variable"
      - "Restart service"
      - "Invalidate existing sessions (optional MVP)"
backup_policy:
  DB_URL: "Daily copy of SQLite file per SOPS.md"
future_migrations:
  - condition: "Postgres adoption"
    changes:
      DB_URL: "postgresql+psycopg://user:pass@host:5432/appdb"
      new_vars:
        DATABASE_POOL_SIZE:
          value: "5"
          required: true
          description: "SQLAlchemy connection pool size"
security_notes:
  - "Do not echo SECRET_KEY in logs"
  - "Ensure directory permissions restrict write to app user"
hash_reference: "Include in STATE.md.env_config_version for drift detection"
```

# Human Notes
- Update version when adding/removing variables or structural changes.
- For each deploy, DevOps validates required_all present.
