```mermaid
erDiagram
    USERS {
      BIGSERIAL id PK
      VARCHAR email
      VARCHAR password_hash
      VARCHAR name
      VARCHAR role
      TIMESTAMPTZ created_at
      TIMESTAMPTZ updated_at
    }
    ORGANIZATIONS {
      BIGSERIAL id PK
      VARCHAR name
      VARCHAR external_billing_id "外部決済プロバイダの顧客ID"
      INT monthly_credit_limit
      TIMESTAMPTZ created_at
      TIMESTAMPTZ updated_at
    }
    ORGANIZATION_MEMBERS {
      BIGINT org_id FK
      BIGINT user_id FK
      VARCHAR role "owner, member"
      TIMESTAMPTZ added_at
    }
    API_KEYS {
      BIGSERIAL id PK
      BIGINT org_id FK
      BIGINT created_by_user_id FK
      CHAR key
      VARCHAR status "active, revoked"
      TIMESTAMPTZ created_at
      TIMESTAMPTZ revoked_at
    }
    LOG_ENTRIES {
      BIGSERIAL id PK
      BIGINT api_key_id FK
      BIGINT org_id FK
      VARCHAR endpoint
      VARCHAR method
      INT status_code
      INT response_time_ms
      TIMESTAMPTZ created_at
    }
    CREDIT_USAGES {
      BIGSERIAL id PK
      BIGINT org_id FK
      INT used_credits
      TEXT description
      TIMESTAMPTZ created_at
    }

    USERS ||--o{ ORGANIZATION_MEMBERS       : participates
    ORGANIZATIONS ||--o{ ORGANIZATION_MEMBERS: has
    ORGANIZATIONS ||--o{ API_KEYS            : issues
    USERS ||--o{ API_KEYS                  : creates
    API_KEYS ||--o{ LOG_ENTRIES             : generates
    ORGANIZATIONS ||--o{ LOG_ENTRIES         : logs
    ORGANIZATIONS ||--o{ CREDIT_USAGES        : consumes
