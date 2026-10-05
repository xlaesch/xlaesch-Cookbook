Programs that have their own process running in the background.

Services can be accessed via `services.msc`. in the "Windows + R" dialog.

```
sc query
```

View running services.

```
sc query type=service state=all
```

View all services available.

```
sc query wuauserv
```

Get information about a service.

```
sc start wuauserv
sc stop wuauserv
```

Start and stop services.