```sql
SELECT * FROM "web_logon_actions"."discovered_schema"."0324_logon_actions" LIMIT 10;

SHOW CREATE TABLE "web_logon_actions"."discovered_schema"."0324_logon_actions";

SELECT count(*) from "web_logon_actions"."discovered_schema"."0324_logon_actions";

CREATE SCHEMA web_logon_actions.<yourname>;

CREATE TABLE logon_actions_structure
WITH
  (
    type = 'iceberg',
    format = 'parquet',
    partitioning = ARRAY['login_date']
  ) AS SELECT * FROM 
  web_logon_actions.discovered_schema."0324_logon_actions";

SELECT * from logon_actions_structure LIMIT 10;

SHOW CREATE TABLE logon_actions_structure; 

SELECT * FROM "logon_actions_structure$partitions";

SELECT * FROM "logon_actions_structure$snapshots";

DELETE FROM logon_actions_structure
WHERE login_success = TRUE and login_attempts <= 2;

SELECT * FROM logon_actions_structure WHERE login_success = TRUE and login_attempts <= 2;

UPDATE logon_actions_structure
SET ip_address = '-1'
WHERE ip_address is NULL;

SELECT * from logon_actions_structure;

SELECT * FROM "logon_actions_structure$snapshots";

SELECT * FROM logon_actions_structure
FOR VERSION AS OF 4740980285783307990
WHERE custkey = 1000643;

select
   a.custkey,
   a.login_success,
   a.login_attempts,
   a.login_date,
   b.first_name,
   b.last_name,
   b.fico,
   c.risk_appetite,
   c.customer_segment
from logon_actions_structure a
inner join sample.burstbank.customer b on cast(a.custkey as varchar) = b.custkey
inner join sample.burstbank.customer_profile c on b.custkey = c.custkey;

SELECT * FROM logon_actions_structure
WHERE custkey = 1000643;

CREATE TABLE logon_actions_consume AS
SELECT
   a.custkey,
   a.login_success,
   a.login_attempts,
   a.login_date,
   b.fico,
   b.first_name,
   b.last_name,
   c.risk_appetite,
   c.customer_segment
FROM logon_actions_structure a
INNER JOIN sample.burstbank.customer b on cast(a.custkey as varchar) = b.custkey
INNER JOIN sample.burstbank.customer_profile c on b.custkey = c.custkey
WHERE a.custkey in (select custkey from logon_actions_structure group by custkey having count(custkey) > 5);



SELECT * from logon_actions_consume LIMIT 10;
