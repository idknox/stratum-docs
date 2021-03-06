---
title: Database Export
category: database
summary: Learn how to export database data from the Stratum CLI.
---

## Export

> ***Note:*** When downloading a database export, be aware that you may be downloading PHI. Proceed with caution and insure that the appropriate disk-level encryption and access controls have been established prior to downloading an export.

The [`db export`](/paas/paas-cli-reference#db-export) command allows you to export and download the current users, ACLs, schema, and data from your running database. To use the `db export` command:

```
$ catalyze db export <service name> <local file path>
```

The filename should be a path to a file (which will be created if it does not exist), not a directory. Example:

```
$ catalyze db export db01 ./mydbdownload.sql
```

For Postgres and MySQL databases, the download will be an SQL file, and for MongoDB, the download will be a gzipped tarball (.tar.gz).

> ***Note:*** Database exports are a **full** backup of your database, meaning that they contain users and permissions as well as schema and data. This means that you should **not** use the `db import` command to pull in a downloaded export into your database.

Exporting a database is like making and downloading an immediate [backup](/stratum/articles/cli-database-backup), and follows the same process listed there.

### See also

* [The Stratum CLI](/stratum/articles/cli-stratum)
* [Database Export](/stratum/articles/cli-database-backup)
