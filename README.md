# Spilo chart

A Reusable [Helm](https://helm.sh/) chart for [Spilo](https://github.com/zalando/spilo) (Highly-Available PostgreSQL database with Patroni), with a default configuration adapted to deployment on the BC Government's OpenShift cluster.

By default, deploys a PostgreSQL cluster with continuous archiving in a `netapp-file-backup` PVC using [wal-g](https://wal-g.readthedocs.io/), enabling [point-in-time recovery](https://www.postgresql.org/docs/14/continuous-archiving.html).

## How to

Are you deploying your application with Helm?

- yes: add spilo as a dependency

```

```

- no:

## Contributing
