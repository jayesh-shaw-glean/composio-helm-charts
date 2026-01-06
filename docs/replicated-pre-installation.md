# Composio Kubernetes Installation Guide

## Prerequisites

Before installing the Helm chart, create the required Kubernetes secrets in the `composio` namespace. These secrets provide credentials and encryption keys needed by the platform and its dependencies.

---

## Database Setup

### Create PostgreSQL Databases and Users

Choose the appropriate SQL script based on your database provider:

#### Self-Hosted PostgreSQL

```sql
CREATE USER composio WITH PASSWORD 'devtesting123';

-- Create databases
CREATE DATABASE composiodb OWNER composio;
CREATE DATABASE temporal OWNER composio;
CREATE DATABASE temporal_visibility OWNER composio;
CREATE DATABASE thermosdb OWNER composio;

-- Grant privileges on thermosdb
\c thermosdb
GRANT ALL PRIVILEGES ON DATABASE thermosdb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Grant privileges on composiodb
\c composiodb
GRANT ALL PRIVILEGES ON DATABASE composiodb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Grant privileges on temporal
\c temporal
GRANT ALL PRIVILEGES ON DATABASE temporal TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Grant privileges on temporal_visibility
\c temporal_visibility
GRANT ALL PRIVILEGES ON DATABASE temporal_visibility TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Grant database creation privilege
ALTER ROLE composio CREATEDB;
```

#### AWS RDS PostgreSQL

For AWS RDS, you cannot use `\c` to switch databases. Execute these commands by connecting to each database separately:

**Connect to the default database (postgres) and run:**

```sql
CREATE USER composio WITH PASSWORD 'devtesting123';

-- Create databases
CREATE DATABASE composiodb OWNER composio;
CREATE DATABASE temporal OWNER composio;
CREATE DATABASE temporal_visibility OWNER composio;
CREATE DATABASE thermosdb OWNER composio;

-- Grant database creation privilege
ALTER ROLE composio CREATEDB;

GRANT ALL PRIVILEGES ON DATABASE thermosdb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

GRANT ALL PRIVILEGES ON DATABASE composiodb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

GRANT ALL PRIVILEGES ON DATABASE temporal TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

GRANT ALL PRIVILEGES ON DATABASE temporal_visibility TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;
```

#### GCP Cloud SQL for PostgreSQL

For GCP Cloud SQL, connect to the default database (postgres) using a superuser account and execute the following script:

```sql
-- Create user with necessary privileges
CREATE USER composio WITH PASSWORD 'devtesting123';
ALTER USER composio WITH CREATEROLE CREATEDB;

-- Grant Cloud SQL superuser role (required for GCP managed instances)
GRANT cloudsqlsuperuser TO composio;
GRANT composio TO postgres;

-- Create databases
CREATE DATABASE composiodb;
CREATE DATABASE temporal;
CREATE DATABASE temporal_visibility;
CREATE DATABASE thermosdb;

-- Configure composiodb
\c composiodb
ALTER DATABASE composiodb OWNER TO composio;
GRANT ALL PRIVILEGES ON DATABASE composiodb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Configure temporal
\c temporal
ALTER DATABASE temporal OWNER TO composio;
GRANT ALL PRIVILEGES ON DATABASE temporal TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Configure thermosdb
\c thermosdb
ALTER DATABASE thermosdb OWNER TO composio;
GRANT ALL PRIVILEGES ON DATABASE thermosdb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

-- Configure temporal_visibility
\c temporal_visibility
ALTER DATABASE temporal_visibility OWNER TO composio;
GRANT ALL PRIVILEGES ON DATABASE temporal_visibility TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;
```

**Note:** The `\c` command works when executing this script through `psql` connected to your GCP Cloud SQL instance. The `cloudsqlsuperuser` role is specific to GCP and provides necessary elevated privileges for managed instances.

#### Azure Database for PostgreSQL

For Azure PostgreSQL, use the following approach:

**Connect to the default database (postgres) and run:**

```sql
CREATE USER composio WITH PASSWORD 'devtesting123';

-- Create databases
CREATE DATABASE composiodb OWNER composio;
CREATE DATABASE temporal OWNER composio;
CREATE DATABASE temporal_visibility OWNER composio;
CREATE DATABASE thermosdb OWNER composio;

-- Grant database creation privilege
ALTER ROLE composio CREATEDB;

GRANT ALL PRIVILEGES ON DATABASE thermosdb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

GRANT ALL PRIVILEGES ON DATABASE composiodb TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

GRANT ALL PRIVILEGES ON DATABASE temporal TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;

GRANT ALL PRIVILEGES ON DATABASE temporal_visibility TO composio;
GRANT ALL PRIVILEGES ON SCHEMA public TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO composio;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON FUNCTIONS TO composio;
```

---

## Kubernetes Secrets Configuration

### Step 1: Create Composio Application Secrets

Replace the placeholder values with your actual tokens, keys, and connection strings:

```bash
kubectl create secret generic <release>-composio-secrets \
  --from-literal=APOLLO_ADMIN_TOKEN=<token> \
  --from-literal=ENCRYPTION_KEY=<encryption-key> \
  --from-literal=TEMPORAL_TRIGGER_ENCRYPTION_KEY=<temporal-key> \
  --from-literal=COMPOSIO_API_KEY=<api-key> \
  --from-literal=JWT_SECRET=<jwt-secret> \
  --from-literal=POSTGRES_URL=<postgres_url> \
  --from-literal=THERMOS_DATABASE_URL=<thermos_database_url> \
  --from-literal=OPENAI_API_KEY=<openai_api_key> \
  -n composio
```

### Step 2: Create Temporal Password Secret

This secret is used by Temporal services for authentication:

```bash
kubectl create secret generic temporal-password-secret \
  --from-literal=password="devtesting123" \
  -n composio
```

---

## TLS Configuration for Temporal

### Important Notes

**If TLS is enabled on your database:**
- You must complete Step 3 below to configure Temporal with TLS
- Skipping these steps will cause the Helm installation to fail

**If TLS is disabled on your database:**
- Skip this section and proceed directly to the Helm chart installation

---

### Step 3: Configure TLS for Temporal

Follow these steps to enable TLS for Temporal when connecting to a managed database such as AWS RDS.

#### 3.1 Download the RDS CA Certificate

Download the AWS RDS CA bundle to establish a trusted connection:

```bash
curl -O https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem
```

#### 3.2 Create a Kubernetes Secret

Create a Kubernetes secret containing the downloaded CA certificate:

```bash
kubectl create secret generic temporal-db-tls-secret \
  --from-file=rds-ca.crt=./global-bundle.pem \
  -n composio
```

This secret will be used by Temporal for TLS host verification.

#### 3.3 Update the Temporal Values File

Update your `values.yaml` file to enable TLS and reference the mounted certificate:

```yaml
default:
  driver: "sql"
  sql:
    driver: "postgres12"
    host: "HOST"
    port: 5432
    database: "temporal"
    user: "composio"
    existingSecret: "composio-composio-secrets"
    maxConns: 20
    maxIdleConns: 20
    maxConnLifetime: "1h"
    tls:
      enabled: true
      disableHostVerification: true
      caFile: /etc/certs/rds-ca.crt

visibility:
  driver: "sql"
  sql:
    driver: "postgres12"
    host: "HOST"
    port: 5432
    database: "temporal_visibility"
    user: "composio"
    existingSecret: "composio-composio-secrets"
    maxConns: 20
    maxIdleConns: 20
    maxConnLifetime: "1h"
    tls:
      enabled: true
      disableHostVerification: true
      caFile: /etc/certs/rds-ca.crt
```

#### 3.4 Mount the Secret in AdminTools

Mount the Kubernetes secret into the `admintools` pod by adding the following configuration:

```yaml
admintools:
  nodeSelector: {}
  tolerations: []
  affinity: {}
  additionalVolumes:
    - name: temporal-db-tls
      secret:
        secretName: temporal-db-tls-secret
  additionalVolumeMounts:
    - name: temporal-db-tls
      mountPath: /etc/certs
      readOnly: true
```

---

## Next Steps

After completing the appropriate configuration steps above, you can proceed with the Helm chart installation.
