
```bash
dropdb -U postgres ext-outss-
pg_dump -Fc ext-outss -U postgres -h 192.168.111.221 -p 30024 > outss.gz
createdb -U postgres ext-outss
pg_restore -U postgres -d ext-outss -j 5 outss.gz
```

