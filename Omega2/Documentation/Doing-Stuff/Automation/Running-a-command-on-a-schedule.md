---
title: Running a Command on a Schedule
layout: guide.hbs
columns: two
devices: [ Omega2 ]
order: 2
---

## Running a Command on a Schedule {#running-a-command-on-a-schedule}

The Omega's firmware has a powerful built in feature called `cron`, that allows you to schedule commands to run at specified intervals. This tutorial will show you how to take advantage of `cron` to automate commands and actions on your Omega.

This can be especially useful for automating projects. For example, you can build a weather monitoring device that displays information on the OLED Expansion and have it update every hour with a few easy steps.




### What is `cron`?

In computing terms, a daemon is a program that runs continuously in the background and answers requests from services. On the Omega, `cron` runs as a daemon and monitors the `crontab`, a file that lists commands that need to run at specified intervals. These commands can be programs, scripts, or anything that you can type in the command line.


### Using `cron`

To learn how to use `cron`, we're going to first write a script that we'll use to execute on a schedule. If you already have a command you'd like to run you can skip the "Writing a Shell Script" subsection.

#### Writing a Shell Script

Let's write a small shell script that will flash your Expansion Dock's RGB LED red, then green, then blue, and just in case you miss it the first time, it'll do it once more after waiting for 5 seconds, and then shut off the RBG LED.

Here's what that looks like:

```
#!/bin/sh -e
expled 0xff0000 #Red
expled 0x00ff00 #Green
expled 0x0000ff #Blue
sleep 5 #wait five seconds
expled 0xff0000 #Red
expled 0x00ff00 #Green
expled 0x0000ff #Blue
expled 0x000000 #Off
```


In the command-line, create a file using `vi /root/rgb-led.sh` and paste the code above into the newly created file. Save and exit the file, and from your command line, enter the following command:

```
chmod +x /root/rgb-led.sh
```

This command will allow your script to be run as a program.

#### Writing the `crontab`

To get started with `cron`, enter the following command on the command line from any directory:

```
crontab -e
```

If you've never before worked with `cron`, an empty file will show up. The first thing we need to do is figure out a command to run at an interval. Let's change the color of the LED on the Expansion Dock using `expled`. In the empty file enter the following:


```
#
*/1 * * * * /root/rgb-led.sh
# Make sure you have this comment at the end of your crontab
```

Save and exit your file. Then restart the `cron` daemon with the following command to apply the changes:

```
/etc/init.d/cron restart
```


Your code can take up to 59 seconds to run because `cron` will wait for the next minute to run (e.g. 6:05:59, 6:06:00)


#### Syntax for `crontab`
So let's break down how `cron` will read the `crontab` file! Here's a quick overview of how cron's syntax works:

```
# * * * * *  command to execute
# ┬ ┬ ┬ ┬ ┬
# │ │ │ │ │
# │ │ │ │ │
# │ │ │ │ └───── day of week (0 - 7) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
# │ │ │ └────────── month (1 - 12)
# │ │ └─────────────── day of month (1 - 31)
# │ └──────────────────── hour (0 - 23)
# └───────────────────────── min (0 - 59)
```

And here are some examples with explanations:

|min| hour | date |month |day of the week | When will my command run? |
|---| --- | --- |--- |--- | --- |
|*/1 | * | * | * | * | Once every minute |
|12 | */3 | * | * | * | Every 3 hours at 12 minutes (8:12, 11:12, 2:12, etc) |
|24 | 6 | 12 | 2,3,4 | * | At 6:24 on the 12th of February, March, and April |
|0 | 0 | 3,8,17 | * | * | At midnight on 3rd, 8th and 17th of every month |

*NOTE: For days of the week, `cron` treats `0` as `Sunday` and setting days of the week to `7` will make you command run every day.*


The ranges of each value (min, hour, etc) are as follows:

|min| hour | date |month |day of the week |
|---| --- | --- |--- |--- | --- |
|0-59 | 0-23 | 1-31 | 1-12 | 0-6 |

*NOTE: The time is written as a 24 hour clock, meaning that 14h is 2:00PM.*


**Your `crontab` file must end with a comment in order for `cron` to run**

For more information on `cron` including more rules and references, you can check out [OpenWRT's guide to Cron](https://wiki.openwrt.org/doc/howto/cron).

#### Saving Output of a `cron` Job to a File (Optional)

When `cron` runs, you won't be able to see any output from your file. You can modify your script or program to save all output to a file rather than print it in the command line, or you can pipe the output of your command to a specific destination with a simple addition to your `crontab` file.

The syntax for piping your command to a file is as follows:

```
<COMMAND> >> <OUTPUT FILE> 2>&1
```

> The `>>` appends the output of your command to the output file. You can use `>` to overwrite the output instead. The `2>&1` is an indicator to the shell script that you want to include the error messages into the output of your command. By default, only standard output is piped.


You can apply this to a `cron` command quite easily.

Enter the `crontab` with:

```
crontab -e
```

and edit the command whose output you want to pipe.

From the earlier example, that would look like:

```
#
*/1 * * * * /root/rgb-led.sh >> /tmp/output.txt 2>&1
#
```

Looking at `/tmp/output.txt` we see:

```
Setting LEDs to: ff0000
Duty: 0 100 100
> Set GPIO16: 1
> Set GPIO15: 1
Setting LEDs to: 00ff00
Duty: 100 0 100
> Set GPIO17: 1
> Set GPIO15: 1
Setting LEDs to: 0000ff
Duty: 100 100 0
> Set GPIO17: 1
> Set GPIO16: 1
Setting LEDs to: ff0000
Duty: 0 100 100
> Set GPIO16: 1
> Set GPIO15: 1
Setting LEDs to: 00ff00
Duty: 100 0 100
> Set GPIO17: 1
> Set GPIO15: 1
Setting LEDs to: 0000ff
Duty: 100 100 0
> Set GPIO17: 1
> Set GPIO16: 1
Setting LEDs to: 000000
Duty: 100 100 100
> Set GPIO17: 1
> Set GPIO16: 1
> Set GPIO15: 1
```

and if we run our script `/root/rgb-led.sh` in the command line we should see the same output:

```
root@Omega-2757:/# /root/rgb-led.sh
Setting LEDs to: ff0000
Duty: 0 100 100
> Set GPIO16: 1
> Set GPIO15: 1
Setting LEDs to: 00ff00
Duty: 100 0 100
> Set GPIO17: 1
> Set GPIO15: 1
Setting LEDs to: 0000ff
Duty: 100 100 0
> Set GPIO17: 1
> Set GPIO16: 1
Setting LEDs to: ff0000
Duty: 0 100 100
> Set GPIO16: 1
> Set GPIO15: 1
Setting LEDs to: 00ff00
Duty: 100 0 100
> Set GPIO17: 1
> Set GPIO15: 1
Setting LEDs to: 0000ff
Duty: 100 100 0
> Set GPIO17: 1
> Set GPIO16: 1
Setting LEDs to: 000000
Duty: 100 100 100
> Set GPIO17: 1
> Set GPIO16: 1
> Set GPIO15: 1
```

You can go back into the `crontab` and comment out the line that runs every minute to stop your Expansion Dock RGB LED from blinking.


### Troubleshooting

* Remember to end your `crontab` file with a comment, or it won't run.
* If your command involves rebooting your Omega please check out this [solution for rebooting with cron](https://wiki.openwrt.org/doc/howto/cron#periodic_reboot_of_a_router) provided by the OpenWRT wiki.
