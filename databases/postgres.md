---
title: PostgreSQL
category: Databases
intro: List of sql queries to manage users, roles
keywords:
  - sql
  - users
  - grant
  - revoke
  - role
  - schema
---

# PG

## Local install on Mac

### Debugging starting issues
{: .-intro}
- [Debugging a missing PostgreSQL connection on macOS](https://thoughtbot.com/blog/macos-postgres-could-not-connect-to-server)

### Start services with brew

```shell
brew services start postgresql
```
### Log locations

```shell
/usr/local/var/log/postgres.log
```

## On AWS
{: .-two-column}

### References
{: .-intro}

- [Setup a new PostgreSQL database on AWS-RDS](http://asheiduk.de/post/setup-pg-on-rds/)
- [RDS: pricing](https://aws.amazon.com/rds/postgresql/pricing/)
- [RDS: instance billing components](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/User_DBInstanceBilling.html)
- [RDS: instances classes perfs](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html)
- [RDS: creating a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)

### Go to setup
`db.m6g.large` + `100go` + `General purpose SSD storage` (avoids extra IOPS costs)

## Roles & Permissions
{: .-two-column}

### List users & their privileges
```sql
SELECT
  usename AS role_name,
  CASE WHEN usesuper
    AND usecreatedb THEN
    CAST('superuser, create database' AS pg_catalog.text)
  WHEN usesuper THEN
    CAST('superuser' AS pg_catalog.text)
  WHEN usecreatedb THEN
    CAST('create database' AS pg_catalog.text)
  ELSE
    CAST('' AS pg_catalog.text)
  END role_attributes
FROM
  pg_catalog.pg_user
ORDER BY
  role_name DESC;
```

### List roles
```sql
SELECT * FROM pg_roles;
```

### List table permissions
```sql
SELECT
  *
FROM
  information_schema.role_table_grants
WHERE
  table_schema = 'public';
```

### List databases a user can connect to
```sql
SELECT datname
FROM pg_database
WHERE has_database_privilege('username', datname, 'CONNECT')
```

### Schema permissions
```sql
SELECT
  r.usename AS grantor,
  e.usename AS grantee,
  nspname,
  privilege_type,
  is_grantable
FROM
  pg_namespace
  JOIN LATERAL (
    SELECT
      *
    FROM
      aclexplode(nspacl) AS x) a ON TRUE
  JOIN pg_user e ON a.grantee = e.usesysid
  JOIN pg_user r ON a.grantor = r.usesysid;
```

### Remove role

Some context for [AWS](https://dba.stackexchange.com/questions/226784/cannot-revoke-permissions-or-drop-user-in-pgsql-aws-rds) and the lack of `superuser` privileges.

```sql
/* Note: does not affect objects within other databases, so repeat for all DBs where the role owns anything. */
REASSIGN OWNED BY username TO postgres;

/* Drops all the objects within the current database that are owned by the role. */
DROP     OWNED BY username;

DROP         ROLE username;
```

The following might be necessary:

```sql
REVOKE ALL PRIVILEGES ON ALL TABLES    IN SCHEMA public FROM username;
REVOKE ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public FROM username;
REVOKE ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA public FROM username;
```

## Application users setup
{: .-one-column}

I tend to use 3 roles: a simple `readonly` one (often going to a read-replica), `write` for row creation or update, `ops` for migrations.

### `readonly` role
```sql
CREATE ROLE role_appname_readonly;
GRANT CONNECT ON DATABASE database_appname_production TO role_appname_readonly;
GRANT USAGE ON SCHEMA public TO role_appname_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO role_appname_readonly;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO role_appname_readonly;

CREATE USER user_appname_readonly WITH PASSWORD 'xxxxxx';
GRANT role_appname_readonly TO user_appname_readonly;
```

### `write` role
```sql
CREATE ROLE role_appname_write;
GRANT CONNECT ON DATABASE database_appname_production TO role_appname_write;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO role_appname_write;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO role_appname_write;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO role_appname_write;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT USAGE ON SEQUENCES TO role_appname_write;

CREATE USER user_appname_write WITH PASSWORD 'xxxxxx';
GRANT role_appname_write TO user_appname_write;
```

### `ops` role
```sql
CREATE ROLE role_appname_ops;
GRANT CONNECT ON DATABASE database_appname_production TO role_appname_ops;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO role_appname_ops;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO role_appname_ops;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO role_appname_ops;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT USAGE ON SEQUENCES TO role_appname_ops;

CREATE USER user_appname_ops WITH PASSWORD 'xxxxxx';
GRANT role_appname_ops TO user_appname_ops;
```
On your local setup, in order to be able to run all migrations, add:
```sql
ALTER USER user_appname_ops CREATEDB;
```
Note: in PostgreSQL, only the owner of a database can drop a database. This is a non issue in production, as on AWS the db already exists and won't be owned by `user_appname_ops`

### References
{: .-intro}

- [Managing PostgreSQL users and roles](https://aws.amazon.com/blogs/database/managing-postgresql-users-and-roles/)
- [How to create a read-only user?](https://tableplus.com/blog/2018/04/postgresql-how-to-create-read-only-user.html)

### Password generation

- 8 to 128 characters.
- Must url encode special characters.

```
pwgen 128 3
```

- https://jasonaowen.net/blog/2017/Feb/09/aws-postgresql-rds-passwords/
