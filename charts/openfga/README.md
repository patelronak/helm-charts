# OpenFGA Helm Chart

This is a Helm chart to deploy [OpenFGA](https://github.com/openfga/openfga) - a high performance and flexible authorization/permission engine built for developers and inspired by Google Zanzibar.

## TL;DR

```sh
helm repo add openfga https://openfga.github.io/helm-charts
helm install openfga openfga/openfga
```

## Installing the Chart via Helm Repository

To install the chart with the release name `openfga`:

```sh
helm repo add openfga https://openfga.github.io/helm-charts
helm install openfga openfga/openfga
```

This will deploy a 3-replica deployment of OpenFGA on the Kubernetes cluster using the default configurations for OpenFGA. For more information on the default values, please see the official [OpenFGA documentation](https://openfga.dev/docs/getting-started/setup-openfga/docker#configuring-the-server). The [Chart Parameters](#chart-parameters) section below lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Installing the chart via OCI Image

This chart is also available for installation from the GitHub OCI registry. It requires helm 3.8+.
To pull from the GitHub OCI registry, run:

```sh
helm install openfga -f values.yaml oci://ghcr.io/openfga/helm-charts
```

## Customization

If you wish to customize the OpenFGA deployment you may supply paremeters such as the ones listed in the [values.yaml](/charts/openfga/values.yaml).

### Installing with Custom Common Labels

You can specify custom labels to insert into resources inline or via Values files:

```sh
helm install openfga openfga/openfga \
  --set-json 'commonLabels={"app.example.com/domain": "example", "app.example.com/system": "permissions"}'
```

```yaml
commonLabels:
  app.example.com/system: permissions
  app.example.com/domain: example
```

### Installing with Postgres

> **Deprecation Notice**: The bundled Bitnami PostgreSQL sub-chart now uses the [legacy archive repository](https://github.com/bitnami/charts/issues/35164) which is no longer actively maintained or receiving security updates. It is provided for backwards compatibility only and will be removed in a future release. For new deployments, we recommend deploying your database separately.

If you already have a Postgres deployment, connect OpenFGA to it by providing the `datastore.uri` parameter:

```sh
helm install openfga openfga/openfga \
  --set datastore.engine=postgres \
  --set datastore.uri="postgres://postgres:password@postgres.default.svc.cluster.local:5432/openfga?sslmode=disable"
```

If you do not have an existing Postgres deployment, you can use the bundled sub-chart:

```sh
helm install openfga openfga/openfga \
  --set datastore.engine=postgres \
  --set datastore.uri="postgres://postgres:password@openfga-postgresql.default.svc.cluster.local:5432/postgres?sslmode=disable" \
  --set postgresql.enabled=true \
  --set postgresql.auth.postgresPassword=password \
  --set postgresql.auth.database=postgres
```

### Installing with MySQL

> **Deprecation Notice**: The bundled Bitnami MySQL sub-chart now uses the [legacy archive repository](https://github.com/bitnami/charts/issues/35164) which is no longer actively maintained or receiving security updates. It is provided for backwards compatibility only and will be removed in a future release. For new deployments, we recommend deploying your database separately.

If you already have a MySQL deployment, connect OpenFGA to it by providing the `datastore.uri` parameter:

```sh
helm install openfga openfga/openfga \
  --set datastore.engine=mysql \
  --set datastore.uri="root:password@tcp(mysql.default.svc.cluster.local:3306)/openfga?parseTime=true"
```

If you do not have an existing MySQL deployment, you can use the bundled sub-chart:

```sh
helm install openfga openfga/openfga \
  --set datastore.engine=mysql \
  --set datastore.uri="root:password@tcp(openfga-mysql.default.svc.cluster.local:3306)/mysql?parseTime=true" \
  --set mysql.enabled=true \
  --set mysql.auth.rootPassword=password \
  --set mysql.auth.database=mysql
```

### Using an existing secret for Postgres or MySQL

If you have an existing secret with the connection details for Postgres or MySQL, you can reference the secret in the values file. For example, say you have created the following secret for Postgres:

```sh
kubectl create secret generic my-postgres-secret \
  --from-literal=uri="postgres://postgres.postgres:5432/postgres?sslmode=disable" \
  --from-literal=username=postgres --from-literal=password=password
```

You can reference this secret in the values file as follows:

```yaml
datastore:
  engine: postgres
  existingSecret: my-postgres-secret
  secretKeys:
    uriKey: uri
    usernameKey: username
    passwordKey: password
```

You can also mix and match both static config and secret references. When the secret key is defined, the static config will be ignored. The following example shows how to reference the secret for username and password, but provide the URI statically:

```yaml
datastore:
  engine: postgres
  uri: "postgres://postgres.postgres:5432/postgres?sslmode=disable"
  existingSecret: my-postgres-secret
  secretKeys:
    usernameKey: username
    passwordKey: password
```

## Uninstalling the Chart

To uninstall/delete the `openfga` deployment:

```sh
helm uninstall openfga
```

## Development

If you are developing or building the chart locally, you need to add the Bitnami legacy archive repository before running `helm dep update`:

```sh
helm repo add bitnami-legacy https://raw.githubusercontent.com/bitnami/charts/archive-full-index/bitnami
helm dep update charts/openfga
```

## Chart Parameters

Take a look at the Chart [values schema reference](https://artifacthub.io/packages/helm/openfga/openfga?modal=values-schema) for more information on the chart values that can be configured. Chart values that are null will default to the server specific default values. For more information on the server defaults please see the [official server configuration documentation](https://openfga.dev/docs/getting-started/setup-openfga/docker#configuring-the-server).
