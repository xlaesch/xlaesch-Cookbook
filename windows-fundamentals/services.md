Windows services are programs with their own processes running in the background.

# Services Console

Open **Windows + R**, then enter `services.msc`.

# Query Services

```powershell
# view running services
sc.exe query

# view all available services
sc.exe query type=service state=all

# get information about a specific service
sc.exe query <service_name>
```

# Start and Stop a Service

```powershell
# start a service
sc.exe start <service_name>

# stop a service
sc.exe stop <service_name>
```
