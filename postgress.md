

[postgress basic tutorials](https://www.postgresqltutorial.com/)

[postgresql-administration](https://www.postgresqltutorial.com/postgresql-administration/)


[postgresss offficial Tutorials](https://www.postgresql.org/docs/online-resources/)



[AWS postgress workshops](https://catalog.us-east-1.prod.workshops.aws/workshops/2a5fc82d-2b5f-4105-83c2-91a1b4d7abfe/en-US/4-advanced)

[link to below Commnads](https://aws.amazon.com/blogs/database/managing-postgresql-users-and-roles/)

[pstgress parameter ref](https://postgresqlco.nf/doc/en/param/)

```bash
-- Revoke privileges from 'public' role
REVOKE CREATE ON SCHEMA public FROM PUBLIC;
REVOKE ALL ON DATABASE mydatabase FROM PUBLIC;

-- Read-only role
CREATE ROLE readonly;
GRANT CONNECT ON DATABASE mydatabase TO readonly;
GRANT USAGE ON SCHEMA myschema TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA myschema TO readonly;
ALTER DEFAULT PRIVILEGES IN SCHEMA myschema GRANT SELECT ON TABLES TO readonly;

-- Read/write role
CREATE ROLE readwrite;
GRANT CONNECT ON DATABASE mydatabase TO readwrite;
GRANT USAGE, CREATE ON SCHEMA myschema TO readwrite;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA myschema TO readwrite;
ALTER DEFAULT PRIVILEGES IN SCHEMA myschema GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO readwrite;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA myschema TO readwrite;
GRANT SELECT ON ALL SEQUENCES IN SCHEMA public TO postgres_ro_group;
ALTER DEFAULT PRIVILEGES IN SCHEMA myschema GRANT USAGE ON SEQUENCES TO readwrite;

-- Users creation
CREATE USER reporting_user1 WITH PASSWORD 'some_secret_passwd';
CREATE USER reporting_user2 WITH PASSWORD 'some_secret_passwd';
CREATE USER app_user1 WITH PASSWORD 'some_secret_passwd';
CREATE USER app_user2 WITH PASSWORD 'some_secret_passwd';

-- Grant privileges to users
GRANT readonly TO reporting_user1;
GRANT readonly TO reporting_user2;
GRANT readwrite TO app_user1;
GRANT readwrite TO app_user2;

--
-- Superuser 
--

-- Create a final user with password
CREATE USER postgres_adm WITH PASSWORD 'secret';
GRANT rds_superuser to postgres_adm;

```


Note: By Default User should not have access to delete object, but if he has created the table he will be the owner and he will be having default of delete objects and table as well, so create a table with admin users, donot create with usage dbname 


tunning Postgress will help to utilize postgressfull, but know the imapact of this before 

[aws rds parameter groups](https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/tuning-postgresql-parameters/tuning-postgresql-parameters.pdf)
[postgress official Documnet for configurations](https://www.postgresql.org)
[PG tune ](https://pgtune.leopard.in.ua/)




# create  a database
```bash
CREATE DATABASE database_name
WITH
   [OWNER =  role_name]
   [TEMPLATE = template]
   [ENCODING = encoding]
   [LC_COLLATE = collate]
   [LC_CTYPE = ctype]
   [TABLESPACE = tablespace_name]
   [ALLOW_CONNECTIONS = true | false]
   [CONNECTION LIMIT = max_concurrent_connection]
   [IS_TEMPLATE = true | false ];

ALTER DATABASE name WITH option;
ALTER DATABASE database_name RENAME TO new_name;

ALTER DATABASE scm OWNER TO steve;
# to validate the database scm
\l scm

#change DB name
ALTER DATABASE db RENAME TO newdb;

# drop database with force
DROP DATABASE test WITH (FORCE)
```

# retrive all Connections to the DB
```bash
SELECT  * FROM pg_stat_activity WHERE datname = 'db';


SELECT datname, pid, usename, application_name, client_addr,client_port  FROM pg_stat_activity WHERE datname ='db';
```


# terminate Connections to the DB
```bash
SELECT
    pg_terminate_backend (pid)
FROM
    pg_stat_activity
WHERE
    datname = 'db';
```


# copy data 

```bash
# to the same server
CREATE DATABASE targetdb  WITH TEMPLATE sourcedb;

# copy data to the diffrent server
pg_dump -C -h localhost -U postgres dvdrental | psql -h remote -U postgres dvdrental
```

#Note: while doing coping data active connections make gace error as DB is using by some other users

# size 

```bash
@ Size of only Table
SELECT pg_size_pretty (pg_relation_size('actor')) size;


# get the size of total indexes etc...
SELECT
    pg_size_pretty (
        pg_total_relation_size ('actor')
    ) size;

# post gress database size
SELECT
    pg_size_pretty (
        pg_database_size ('dvdrental')
    ) size;


# size of Each Database size
SELECT
    pg_database.datname,
    pg_size_pretty(pg_database_size(pg_database.datname)) AS size
FROM pg_database;

# index size
SELECT
    pg_size_pretty (pg_indexes_size('actor')) size;

#table value size 
SELECT
    pg_size_pretty (
        pg_tablespace_size ('pg_default')
    ) size;
```


# schema

```bash
CREATE SCHEMA schema_name [IF NOT EXISTS]
    CREATE TABLE table_name1 (...)
    CREATE TABLE table_name2 (...)
    CREATE VIEW view_name1
    AUTHORIZATION username
        SELECT select_list FROM table_name1;

# list all schemas in current database
SELECT *  FROM pg_catalog.pg_namespace ORDER BY nspname;

#check scema
SELECT current_schema();

# check order of schema
SHOW search_path;

# set order is schema
SET search_path TO sales, public;

# rename schema
ALTER SCHEMA schema_name RENAME TO new_name;

# change ownership
ALTER SCHEMA finance  OWNER TO postgres;

# to check the user created schema
SELECT * 
FROM 
    pg_catalog.pg_namespace
WHERE 
    nspacl is NULL AND
    nspname NOT LIKE 'pg_%'
ORDER BY 
    nspname;

# if exists
DROP SCHEMA IF EXISTS finance, marketing;

# if schema has dependents
DROP SCHEMA scm CASCADE;
```
Note: what ever you do by default you will be in 1st schema

# Roles
```bash
CREATE ROLE dba 
CREATEDB 
LOGIN  | WITH LOGIN NOINHERIT   # if donot want all permissions of group_role 
PASSWORD 'securePass1'
CONNECTION LIMIT 1000
VALID UNTIL '2050-01-01';

# list roles
\du or \du role_name

GRANT role_name TO user

ALTER ROLE role_name TO new_name;

REVOKE group_role FROM role;

# switch to other role to debug Permissios
SELECT current_role;

# reset the orginal one
RESET ROLE;

# check the CURRENT_USER
SELECT CURRENT_USER;
SELECT SESSION_USER;    # while conneting to DB which user you have connected to

#change roles 
ALTER ROLE role_name [WITH] option;


# drop role
-- execute these statements in the database that contains
-- the object owned by the target role
REASSIGN OWNED BY target_role TO another_role;

DROP OWNED BY target_role;

-- drop the role
DROP ROLE target_role;

```
# Users

```bash
# list users
\du or \du+

SELECT usename AS role_name,
  CASE 
     WHEN usesuper AND usecreatedb THEN 
	   CAST('superuser, create database' AS pg_catalog.text)
     WHEN usesuper THEN 
	    CAST('superuser' AS pg_catalog.text)
     WHEN usecreatedb THEN 
	    CAST('create database' AS pg_catalog.text)
     ELSE 
	    CAST('' AS pg_catalog.text)
  END role_attributes
FROM pg_catalog.pg_user
ORDER BY role_name desc;
```

# Grant
```bash
GRANT privilege_list | ALL 
ON  table_name | ALL TABLES IN SCHEMA schema_name
TO  role_name;


privilege_list --> SELECT, INSERT, UPDATE, DELETE,TRUNCATE

# SELECT
SELECT * FROM candidates;

#INSERT
INSERT INTO candidates(first_name, last_name, email, phone)
VALUES('Joe','Com','joe.com@example.com','408-111-2222');

# revoke 
REVOKE privilege | ALL
ON TABLE table_name |  ALL TABLES IN SCHEMA schema_name
FROM role_name;
```

Raw level security 

```bash
ALTER TABLE departments ENABLE ROW LEVEL SECURITY;

CREATE POLICY department_managers
ON departments 
TO managers
USING (manager = current_user);

```

```bash 
psql -h host -d database -U user -W

# show database
\l+ oror \l or  SELECT datname FROM pg_database;


# swith to other database with username
\c dbname username

# list all tables
\dt

# Describe table
\d table_name

# list avilable schemas
\dn

# list functions
\df

# list views
\dv

# list users and roles
\du

# list at tables
\d table_name or \d+ table_name

# show tables by pg_catalog schema
SELECT *
FROM pg_catalog.pg_tables
WHERE schemaname != 'pg_catalog' AND 
    schemaname != 'information_schema';



# describe table by information_schema
SELECT 
  column_name, 
  data_type, 
  character_maximum_length, 
  is_nullable, 
  column_default 
FROM 
  information_schema.columns 
WHERE 
  table_name = 'film';

# prvios command
\g

# hitory
\s

# turn on  and turn off query executio
\timing


SELECT CURRENT_USER;

SELECT version();

```


```bash
To terminate the active connections to the dvdrental database, you use the following query:
SELECT pg_terminate_backend (pid)
FROM pg_stat_activity
WHERE datname = 'dvdrental';
```

