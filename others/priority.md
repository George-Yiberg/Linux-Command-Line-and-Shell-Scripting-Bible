Nice value is a user-space and priority PR is the process's actual priority that use by Linux kernel. In linux system priorities are 0 to 139 in which 0 to 99 for real time and 100 to 139 for users. nice value range is -20 to +19 where -20 is highest, 0 default and +19 is lowest. relation between nice value and priority is :

```
PR = 20 + NI
```

so , the value of `PR = 20 + (-20 to +19)` is 0 to 39 that maps 100 to 139.

According to top manual:

> PR -- Priority The scheduling priority of the task. If you see 'rt' in this field, it means the task is running under 'real time' scheduling priority.

NI is nice value of task.

> NI -- Nice Value The nice value of the task. A negative nice value means higher priority, whereas a positive nice value means lower priority.Zero in this field simply means priority will not be adjusted in determining a task's dispatch-ability

Edit: By default when a program is launched in Linux, it gets launched with the priority of '0'. However you can change the priority of your programs by either of the following methods.

1. You can launch a program with your required priority using

   ```
   nice -n nice_value program_name
   ```

2. you can also change the priority of an already running process using

   ```
   renice -n nice_value -p process_id
   ```







## What is Priority and Why Should I Care?

When talking about processes priority is all about managing processor time. The Processor or CPU is like a human juggling multiple tasks at the same time. Sometimes we can have enough room to take on multiple projects. Sometimes we can only focus on one thing at a time. Other times something important pops up and we want to devote all of our energy into solving that problem while putting less important tasks on the back burner.

In Linux we can set guidelines for the CPU to follow when it is looking at all the tasks it has to do. These guidelines are called niceness or nice value. The Linux niceness scale goes from -20 to 19. The lower the number the more priority that task gets. If the niceness value is high number like 19 the task will be set to the lowest priority and the CPU will process it whenever it gets a chance. The default nice value is zero.

By using this scale we can allocate our CPU resources more appropriately. Lower priority programs that are not important can be set to a higher nice value, while high priority programs like daemons and services can be set to receive more of the CPU’s focus. You can even give a specific user a lower nice value for all of his/her processes so you can limit their ability to slow down the computer’s core services.

[Source](http://www.nixtutor.com/linux/changing-priority-on-linux-processes/)

Set the priority for new processes with `nice`, eg

```
nice -n 10 firefox
```

for existing processes

```
renice 10 -p $(pgrep firefox)
```

To set the priority `<0` you need `sudo`, eg:

```
renice -1 -p $(pgrep firefox)
renice: failed to set priority for 2769 (process ID): Permission denied
```

but not for a priority `>=0`

------

*Example*

```
% ps -o pid,comm,pri,nice -p $(pgrep firefox)
  PID COMMAND         PRI  NI
 2769 firefox          19   0

% renice 10 -p 2769     # note, we don't need sudo here
2769 (process ID) old priority 0, new priority 10

% ps -o pid,comm,pri,nice -p $(pgrep firefox)
  PID COMMAND         PRI  NI
 2769 firefox           9  10

% sudo renice -19 -p 2769                    
 2769 (process ID) old priority 10, new priority -19

% ps -o pid,comm,pri,nice -p $(pgrep firefox)
  PID COMMAND         PRI  NI
 2769 firefox          38 -19
```

------

*Other example*

To renice all running processes for a specific user

```
renice 20 -u user_name
```

