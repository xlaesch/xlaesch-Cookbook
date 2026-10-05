Scheduled tasks execute operations at specified times or intervals. Task Scheduler manages these tasks on Windows.

# Task Scheduler Console

Open **Windows + R**, then enter `taskschd.msc`.

# Query Tasks

```powershell
# view all scheduled tasks
schtasks

# view a specific task
schtasks /Query /TN "<task_name>"
```

# Enable a Task

```powershell
# enable a scheduled task
schtasks /Change /ENABLE /TN "<task_name>"
```

# Run a Task

```powershell
# run a scheduled task
schtasks /Run /TN "<task_name>"
```

# End a Task

```powershell
# terminate a scheduled task
schtasks /End /TN "<task_name>"
```

# Delete a Task

```powershell
# delete a scheduled task
schtasks /Delete /TN "<task_name>"
```
