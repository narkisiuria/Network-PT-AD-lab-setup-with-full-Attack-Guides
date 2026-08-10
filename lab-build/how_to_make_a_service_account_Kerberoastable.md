## In order to make a service account officially Kerberoastable ##

folow these steps:

1: right click the Start button in the DC.
2: Select: Windows PowerShell (ADMIN).
3: run this command:

```bash
setspn -A MSSQLSvc/sql-server.[YOUR DOMAIN NAME]:1433 svc-sql 
```

If it turend out with no errors then the service is officially Kerberoastable.