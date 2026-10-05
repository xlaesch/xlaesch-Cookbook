
Scheduled task is the execution of certain operations on the system at certain time intervals or at certain times. 

On windows we can manage scheduled tasks via "Windows + R" and `taskschd.msc`

```
schtasks
```

To view all sheduled tasks.

```
schtasks /Query /TN TrainingTask
```

View a specific task.

```
schtasks /Change /ENABLE /TN TrainingTask
```

Enable a scheduled task.

```
schtasks /Run /TN TrainingTask
```

Run the scheduled task.

```
schtasks /End /TN TrainingTask
```

Terminating the scheduled task.

```
schtasks /Delete /TN TrainingTask
```
