Migrate to RDS [reference](https://www.digitalocean.com/community/tutorials/how-to-backup-postgresql-databases-on-an-ubuntu-vps)

1.Backup<br/>`pg_dump -Fc "database name" > backup.dump`

2.Restore to RDS<br/>`pg_restore -h "end point" -U "user name" -d "database name" backup.dump`

3.Set up [Postgis](https://gist.github.com/matthewberryman/7689766b5f94a5499d8c) on RDS

```
create extension postgis;
create extension fuzzystrmatch;
create extension postgis_tiger_geocoder;
create extension postgis_topology;

alter schema tiger owner to rds_superuser;
alter schema tiger_data owner to rds_superuser;
alter schema topology owner to rds_superuser;

CREATE FUNCTION exec(text) returns text language plpgsql volatile AS $f$ BEGIN EXECUTE $1; RETURN $1; END; $f$;

SELECT exec('ALTER TABLE ' || quote_ident(s.nspname) || '.' || quote_ident(s.relname) || ' OWNER TO rds_superuser;')
  FROM (
    SELECT nspname, relname
    FROM pg_class c JOIN pg_namespace n ON (c.relnamespace = n.oid) 
    WHERE nspname in ('tiger','topology') AND
    relkind IN ('r','S','v') ORDER BY relkind = 'S')
s;

```

