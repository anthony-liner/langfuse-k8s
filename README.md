![GitHub Banner](https://github.com/langfuse/langfuse-k8s/assets/2834609/2982b65d-d0bc-4954-82ff-af8da3a4fac8)

# langfuse-k8s

[langfuse/langfuse-k8s](https://github.com/langfuse/langfuse-k8s)를 LINER 환경에 맞게 배포하기 위한 fork입니다.

## Helm Chart

We provide an Helm chart that helps you deploy Langfuse on Kubernetes. This Helm chart could use an external Postgres server or deploy one for you, thanks to [Bitnami Postgres Helm chart](https://github.com/bitnami/charts/tree/main/bitnami/postgresql).

### GCP 사전 설정 항목

- VPC network internal static IP address([langfuse-static-ip](<https://console.cloud.google.com/networking/addresses/list?referrer=search&authuser=1&project=liner-219011&pageState=(%22allAddressesTable%22:(%22f%22:%22%255B%257B_22k_22_3A_22_22_2C_22t_22_3A10_2C_22v_22_3A_22_5C_22langfuse_5C_22_22%257D%255D%22))>))
- Cloud DNS private zone A record([dev-langfuse.getliner.com](https://console.cloud.google.com/net-services/dns/zones/private-getliner/rrsets/dev-langfuse.getliner.com./A/view?authuser=1&project=liner-219011))
- Certificate Manager SSL certificate([internal-2-wildcard-getliner-com](https://console.cloud.google.com/security/ccm/lbCertificates/details/regions/us-west1/internal-2-wildcard-getliner-com?project=liner-219011&authuser=1))

### Installation

```bash
helm install [NAME] ./charts/langfuse -f liner-values.yaml -n [NAMESPACE]
```

### Upgrading

```bash
helm upgrade [NAME] ./charts/langfuse -f liner-values.yaml -n [NAMESPACE]
```

### Uninstallation

```bash
helm uninstall [NAME] -n [NAMESPACE]
```

### Configuration

The following table lists the useful configurable parameters of the Langfuse chart and their default values.

| Parameter                             | Description                                                                                                                                                                                                                                                                                                                                  | Default          |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| `langfuse.nextauth.url`               | When deploying to production, set the `nextauth.url` value to the canonical URL of your site.                                                                                                                                                                                                                                                | `localhost:3000` |
| `langfuse.nextauth.secret`            | Used to encrypt the NextAuth.js JWT, and to hash email verification tokens.                                                                                                                                                                                                                                                                  | `changeme`       |
| `langfuse.salt`                       | Salt for API key hashing                                                                                                                                                                                                                                                                                                                     | `changeme`       |
| `langfuse.telemetryEnabled`           | Weither or not to enable telemetry (reports basic usage statistics of self-hosted instances to a centralized server).                                                                                                                                                                                                                        | `true`           |
| `langfuse.additionalEnv`              | Dict that allow addition of additional env variables, see [documentation](https://langfuse.com/docs/deployment/self-host#configuring-environment-variables) for details.                                                                                                                                                                     | `[]`             |
| `service.type`                        | Change the default k8s service type deployed with the application                                                                                                                                                                                                                                                                            | `ClusterIP`      |
| `service.additionalLabels`            | Add additional annotations to the service deployed with the application                                                                                                                                                                                                                                                                      | `[]`             |
| `ingress.enabled`                     | Enable ingress for the application                                                                                                                                                                                                                                                                                                           | `false`          |
| `ingress.annotations`                 | Annotation to add the the deployed ingress                                                                                                                                                                                                                                                                                                   | `[]`             |
| `postgresql.deploy`                   | Enable postgres deployment (via Bitnami Helm Chart). If you want to use a postgres server already deployed (or a managed one), set this to false                                                                                                                                                                                             | `true`           |
| `postgresql.auth.username`            | Username to use to connect to the postgres database deployed with Langfuse. In case `postgresql.deploy` is set to `true`, the user will be created automatically.                                                                                                                                                                            | `postgres`       |
| `postgresql.auth.password`            | Password to use to connect to the postgres database deployed with Langfuse. In case `postgresql.deploy` is set to `true`, the password will be set automatically.                                                                                                                                                                            | `postgres`       |
| `postgresql.auth.database`            | Database name to use for Langfuse.                                                                                                                                                                                                                                                                                                           | `langfuse`       |
| `postgresql.host`                     | If `postgresql.deploy` is set to false, hostname of the external postgres server to use (mandatory)                                                                                                                                                                                                                                          | `nil`            |
| `postgresql.directUrl`                | If `postgresql.deploy` is set to false, Connection string of your Postgres database used for database migrations. Use this if you want to use a different user for migrations or use connection pooling on DATABASE_URL. For large deployments, configure the database user with long timeouts as migrations might need a while to complete. | `nil`            |
| `postgresql.shadowDatabaseUrl`        | If your database user lacks the CREATE DATABASE permission, you must create a shadow database and configure the "SHADOW_DATABASE_URL". This is often the case if you use a Cloud database. Refer to the Prisma docs for detailed instructions.                                                                                               | `nil`            |
| `postgresql.primary.persistence.size` | Disk request for the postgres database deployed with Langfuse. Effective only if `postgresql.deploy` is set to true                                                                                                                                                                                                                          | `8Gi`            |

#### Examples:

##### With an external Postgres server

```yaml
langfuse:
  nextauth:
    url: localhost:3000
    secret: changeme
  salt: changeme
  telemetryEnabled: true
service:
  type: ClusterIP
  additionalLabels: []
ingress:
  enabled: false
  annotations: []
postgresql:
  deploy: false
  auth:
    username: postgres
    password: changeme
    database: langfuse
  host: my-external-postgres-server.com
  directUrl: postgres://user:password@my-external-postgres-server.com
  shadowDatabaseUrl: postgres://user:password@my-external-postgres-server.com
```

##### Deploy a Postgres server at the same time

```yaml
langfuse:
  nextauth:
    url: localhost:3000
    secret: changeme
  salt: changeme
  telemetryEnabled: true
service:
  type: ClusterIP
ingress:
  enabled: false
postgresql:
  deploy: true
  auth:
    password: changeme
```

## Repository Structure

- `examples` directory contains example `yaml` configurations
- `charts/langfuse` directory contains Helm chart for deploying Langfuse with an associated database

Please feel free to contribute any improvements or suggestions.

Langfuse deployment docs: https://langfuse.com/docs/deployment/self-host
