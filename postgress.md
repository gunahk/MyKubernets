

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
```


Note: By Default User should not have access to delete object, but if he has created the table he will be the owner and he will be having default of delete objects and table as well, so create a table with admin users, donot create with usage dbname 


tunning Postgress will help to utilize postgressfull, but know the imapact of this before 

[aws rds parameter groups](https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/tuning-postgresql-parameters/tuning-postgresql-parameters.pdf)


[postgress official Documnet for configurations](https://www.postgresql.org)
