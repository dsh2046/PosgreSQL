Migrate to RDS

1.Backup 
`pg_dump -Fc "database name" > backup.dump`

2.Restore to RDS
`pg_restore -h "end point" -U "user name" -d "database name" backup.dump`

3.Set up [Postgis](https://gist.github.com/matthewberryman/7689766b5f94a5499d8c) on RDS

