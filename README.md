# Spilo chart

A Reusable [Helm](https://helm.sh/) chart for [Spilo](https://github.com/zalando/spilo) (Highly-Available PostgreSQL database with Patroni), with a default configuration adapted to deployment on the BC Government's OpenShift cluster.

By default, deploys a PostgreSQL cluster with continuous archiving in a `netapp-file-backup` PVC using [wal-g](https://wal-g.readthedocs.io/), enabling [point-in-time recovery](https://www.postgresql.org/docs/14/continuous-archiving.html).

## Installation

### You are deploying your application server with Helm

1. Add spilo as a dependency in your `Chart.yaml`

```yaml
dependencies:
  - name: spilo
    version: 0.1.0
    repository: https://bcgov.github.io/spilo-chart
    # by default, the object created will be named <your-app>-spilo. You can use an alias to override the -spilo suffix
    alias: postgres
```

2. Update your application (don't forget to update your helm dependencies with `helm dep up`)

### You are not deploying your application with Helm

If this is your first time using helm, check out the [quickstart guide](https://helm.sh/docs/intro/quickstart/). The steps below are similar to the one from the quickstart guide, but specifically for this chart

`$ helm repo add spilo-chart https://bcgov.github.io/spilo-chart`

Once the repo is added, you can check the available charts with:

```
$ helm search repo spilo-chart
NAME                CHART VERSION   APP VERSION   DESCRIPTION
spilo-chart/spilo   0.1.0           2.1-p4        Highly available elephant herd: HA PostgreSQL c...
```

Then, install an instance of the chart with:

```
$ helm install -n my-namespace my-spilo-instance spilo-chart/spilo

NAME: my-spilo-instance
LAST DEPLOYED: Mon Feb 28 15:38:32 2022
NAMESPACE: my-namespace
STATUS: deployed
REVISION: 1
```

### Configuration and default behaviour

You can find an exhaustive list of the configurable settings in `charts/spilo/values.yaml`.

**Accessing the database cluster**

This chart creates two services, allowing to access the leader (read/write connection) and replica (read-only connection) pods, respectively. The `postgres` user's password is available in the secret created by this chart under the `superuser-password` key. Note that you should only use the `postgres` user to create the user that will actually be used by your application, as [applications should not use superusers](https://patroni.readthedocs.io/en/latest/README.html#applications-should-not-use-superusers).

**PostgreSQL Version**

The `PGVERSION` environment variable, set by the `postgresMajorVersion` value (defaults to `14`), defines which version of PostgreSQL Spilo will use. The following versions are supported: `9.6`, `10`, `11`, `12`, `13`, `14`.

**PostgreSQL Major Upgrade**

When updating the `PGVERSION`, Spilo will not automatically upgrade your cluster, and will continue to run with the previous major version of PostgreSQL. To upgrade your cluster, open a terminal in the leader pod, and run `python3 /scripts/inplace_upgrade.py N`, where `N` is your number of replicas

**Continuous Archiving**

By default, this chart will create an `<instanceName>-archive` PVC using the `netapp-file-backup` storage class, and create daily base backups to in this PVC, using `wal-g`. It also uses continuous archiving of WAL files, allowing you to restore to a specific point in time between base backups (see the [PostgreSQL documentation](https://www.postgresql.org/docs/14/continuous-archiving.html) for more information)

**Restore**

The restore process is included [here](docs/restore.md)