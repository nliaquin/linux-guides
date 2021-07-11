# Systemd
Full coverage of everything you need to know about systemd. This will be a somewhat longer and comprehensive guide. Whether we like it or not, systemd is here to stay. A lot of people don't fully grasp the weight of systemd, and this might be due to new Linux users not knowing what it was like with older init systems. Those who have been around for some time may understand that systemd is the now widely-adopted init system of most major Linux distributions. It's a very monolithic program that handles all of the startup routines and manages services such as journaling, networking, etc. For context of the above statement on liking it or not, many Linux users oppose systemd on the fact that it breaks the unix standard of creating programs that serve an atomic purpose. Older init systems did not carry out the same amount of work systemd does, and removing systemd from a distro would require layers of reworking daemon management, journaling, and so forth. Regardless of our opinions, systemd is here to stay, and it has taken over. Therefore, it would be wise to learn it.

### Table of contents
 - [](#)
 - [](#)
 - [](#)
 - [](#)
 - [](#)
 - [](#)


### What is init and systemd?
To preface, init is not a specific program, it is a type of program. Systemd is an example of an init system, as is launchd, OpenRC, etc.

In brief, an init system is what manages daemons and services alongside the within the OS sandbox. This traditionally serves the atomic purpose of any init system as defined for unix systems. Windows and Mac also have init systems of their own which function in the way Microsoft and Apple have specified them to do so. Init, in BSD and Linux' case, is the very first process that is started when the operating system sandbox is instantiated. You can verify this on Linux by using the ps command and looking for the service having the process ID of 1. I should also mention that systemd is exclusive to Linux and is not implemented on BSD.

Init will carry out the launch and management of OS-level programs in a particular order. To break it down, the operating system is started after the boot and mounting processes are finished, which is usually when Grub is done carrying out its set of tasks. Then when it's time for OS-level programs to come into play, the init system is started, and then the init system runs OS-level programs in a particular order as well. The programs that are started by the init system also depends on your distro's run-level. If you use a Linux distro at runlevel 1, or single-user mode, init has less work to do at the start because you don't have a display manager or a desktop environment. If you run at a multi-user runlevel, the init system will have more work to do, as you're likely running lightdm or gdm3, a greeter daemon, a desktop environment with a GUI, etc.

Now that you have a basic understanding of how init systems work, let's talk specifically about systemd. This is a bit more complex than a traditional init system as you may have interpreted in the beginning of the guide. Systemd manages network connection with networkd, kernel logging with journald, logins with logind, etc. It used to be that there were different programs not managed by the init system that run separately and atomically from any other program. This was tricky for some maintainers, as these separate programs sometimes didn't function 100% cohesively, but that's why most major distro maintainers would tweak said programs to serve a better experience in the userspace.

Systemd will manage logins, network connections, kernel modules, mounting hardware, and the list goes on. Systemd also comprises of multiple services under one program, such as the ones I mentioned above. An interface for users to manipulate system services is available, known as systemctl. Systemd also gives interfaces to system time through timedatectl as well. One way you can interpret this is that, as someone creating their own distro, systemd lifts a lot of weight off your shoulders. You won't have to figure out all the different programs you're going to use to carry out all of these tasks since systemd is a one-stop-shop for everything you need. On the other hand, systemd is a bit difficult to make changes to as one maintainer, and while uniformity is a good thing in general, it's taking a lot of options off the table for changing how your distro handles major tasksi which are now controlled by systemd.


### Unit Files
Systemd uses what are defined as 'units' to configure how programs are daemonized and managed. Most major programs come with a unit file, which is part of why systemd's adoption has been a widespread success. These unit files can even be modified and/or created by you. If you wanted to create a program that runs before a user even logs in, you create a unit file for systemd. For instance, I created a program that texts a given phone number every time the computer the program is placed on is started. While it is a multi-platform program that can run on almost any OS today, I still need a unit file that configures how systemd handles this program for Linux.

Unit files are stored in /etc/systemd/system/ and they look a little like the following:

[Unit]
Description=Light Display Manager
Documentation=man:lightdm(1)
Conflicts=getty@tty1.service
After=getty@tty1.service systemd-user-sessions.service plymouth-quit.service acpid.service

[Service]
ExecStart=/usr/bin/lightdm
Restart=always
IgnoreSIGPIPE=no
BusName=org.freedesktop.DisplayManager

[Install]
Alias=display-manager.service


This is the unit file for my display manager service. There is a Unit section for defining specific information on the unit itself, a Service section for program and daemonization information, and an Install section for information on how to manage the program, its dependencies, etc. Here are the different options for unit files (source=https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files):

#### [Unit] Options
Description=: This directive can be used to describe the name and basic functionality of the unit. It is returned by various systemd tools, so it is good to set this to something short, specific, and informative.

Documentation=: This directive provides a location for a list of URIs for documentation. These can be either internally available man pages or web accessible URLs. The systemctl status command will expose this information, allowing for easy discoverability.

Requires=: This directive lists any units upon which this unit essentially depends. If the current unit is activated, the units listed here must successfully activate as well, else this unit will fail. These units are started in parallel with the current unit by default.

Wants=: This directive is similar to Requires=, but less strict. Systemd will attempt to start any units listed here when this unit is activated. If these units are not found or fail to start, the current unit will continue to function. This is the recommended way to configure most dependency relationships. Again, this implies a parallel activation unless modified by other directives.

BindsTo=: This directive is similar to Requires=, but also causes the current unit to stop when the associated unit terminates.

Before=: The units listed in this directive will not be started until the current unit is marked as started if they are activated at the same time. This does not imply a dependency relationship and must be used in conjunction with one of the above directives if this is desired.

After=: The units listed in this directive will be started before starting the current unit. This does not imply a dependency relationship and one must be established through the above directives if this is required.

Conflicts=: This can be used to list units that cannot be run at the same time as the current unit. Starting a unit with this relationship will cause the other units to be stopped.

Condition...=: There are a number of directives that start with Condition which allow the administrator to test certain conditions prior to starting the unit. This can be used to provide a generic unit file that will only be run when on appropriate systems. If the condition is not met, the unit is gracefully skipped.

Assert...=: Similar to the directives that start with Condition, these directives check for different aspects of the running environment to decide whether the unit should activate. However, unlike the Condition directives, a negative result causes a failure with this directive.

#### [Service] Options
ExecStart=: This specifies the full path and the arguments of the command to be executed to start the process. This may only be specified once (except for “oneshot” services). If the path to the command is preceded by a dash “-” character, non-zero exit statuses will be accepted without marking the unit activation as failed.

ExecStartPre=: This can be used to provide additional commands that should be executed before the main process is started. This can be used multiple times. Again, commands must specify a full path and they can be preceded by “-” to indicate that the failure of the command will be tolerated.

ExecStartPost=: This has the same exact qualities as ExecStartPre= except that it specifies commands that will be run after the main process is started.

ExecReload=: This optional directive indicates the command necessary to reload the configuration of the service if available.

ExecStop=: This indicates the command needed to stop the service. If this is not given, the process will be killed immediately when the service is stopped.

ExecStopPost=: This can be used to specify commands to execute following the stop command.

RestartSec=: If automatically restarting the service is enabled, this specifies the amount of time to wait before attempting to restart the service.

Restart=: This indicates the circumstances under which systemd will attempt to automatically restart the service. This can be set to values like “always”, “on-success”, “on-failure”, “on-abnormal”, “on-abort”, or “on-watchdog”. These will trigger a restart according to the way that the service was stopped.

TimeoutSec=: This configures the amount of time that systemd will wait when stopping or stopping the service before marking it as failed or forcefully killing it. You can set separate timeouts with TimeoutStartSec= and TimeoutStopSec= as well.

Type=simple: The main process of the service is specified in the start line. This is the default if the Type= and Busname= directives are not set, but the ExecStart= is set. Any communication should be handled outside of the unit through a second unit of the appropriate type (like through a .socket unit if this unit must communicate using sockets).

Type=forking: This service type is used when the service forks a child process, exiting the parent process almost immediately. This tells systemd that the process is still running even though the parent exited.

Type=oneshot: This type indicates that the process will be short-lived and that systemd should wait for the process to exit before continuing on with other units. This is the default Type= and ExecStart= are not set. It is used for one-off tasks.

Type=dbus: This indicates that unit will take a name on the D-Bus bus. When this happens, systemd will continue to process the next unit.

Type=notify: This indicates that the service will issue a notification when it has finished starting up. The systemd process will wait for this to happen before proceeding to other units.

Type=idle: This indicates that the service will not be run until all jobs are dispatched.

#### [Install] Options:
WantedBy=: The WantedBy= directive is the most common way to specify how a unit should be enabled. This directive allows you to specify a dependency relationship in a similar way to the Wants= directive does in the [Unit] section. The difference is that this directive is included in the ancillary unit allowing the primary unit listed to remain relatively clean. When a unit with this directive is enabled, a directory will be created within /etc/systemd/system named after the specified unit with .wants appended to the end. Within this, a symbolic link to the current unit will be created, creating the dependency. For instance, if the current unit has WantedBy=multi-user.target, a directory called multi-user.target.wants will be created within /etc/systemd/system (if not already available) and a symbolic link to the current unit will be placed within. Disabling this unit removes the link and removes the dependency relationship.

RequiredBy=: This directive is very similar to the WantedBy= directive, but instead specifies a required dependency that will cause the activation to fail if not met. When enabled, a unit with this directive will create a directory ending with .requires.

Alias=: This directive allows the unit to be enabled under another name as well. Among other uses, this allows multiple providers of a function to be available, so that related units can look for any provider of the common aliased name.

Also=: This directive allows units to be enabled or disabled as a set. Supporting units that should always be available when this unit is active can be listed here. They will be managed as a group for installation tasks.

DefaultInstance=: For template units (covered later) which can produce unit instances with unpredictable names, this can be used as a fallback value for the name if an appropriate name is not provided.

Visit https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files to see more options and sections.


### systemctl
This guide is a work in progress. Feel free to contact me, nickolas@nliaquin.xyz, for personal assistance.
