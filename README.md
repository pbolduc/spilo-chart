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

Once the repo is added, you can check the available charts with

```
$ helm search repo spilo-chart
NAME                CHART VERSION   APP VERSION   DESCRIPTION
spilo-chart/spilo   0.1.0           2.1-p4        Highly available elephant herd: HA PostgreSQL c...
```

```
$ helm install -n my-namespace my-spilo-instance spilo-chart/spilo

NAME: my-spilo-instance
LAST DEPLOYED: Mon Feb 28 15:38:32 2022
NAMESPACE: my-namespace
STATUS: deployed
REVISION: 1
```
