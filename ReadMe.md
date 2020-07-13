This software provides a conneciton between the generator digital input
and the Venus generator control logic.
Functionality has been verified on Venus version 2.60 release candidate ~26 - ~31.
Other versions may need rewrites of the two modified files. 
The installer will provide an alert if the stock files differ from the expected ones

The generator digital input in the stock Venus code only displays the stopped/running state in the Device List

If no run conditions are active, the generator ManualStart state will track the generator digital input.
Any automatic run condition such as low SOC will prevail even if the generator digital input is in it's stopped state.
In this case, "External Override Generator is NOT running" is displayed on the Overview Generator page.

These changes REPLACE /opt/victronenergy/dbus-generator-starter/startstop.py
and /opt/victronenergy/gui/qml/OverviewGenerator.qml files

"Running" or "Stopped" is displayed in the generator icon on the Generator Overview page if the digital input so indicates.

Setup:

You will need root access to the Venus device. Instructions can be found here:
https://www.victronenergy.com/live/ccgx:root_access
The root password needs to be reentered following a Venus update.
Setting up an authorization key (see documentation referenced above) will save time and avoid having to reset the root password after each update.

A setup script is provided that runs on either a unix host or on the Venus device itself.
It supports the following actions:
    Activate installs GUI and startstop.py modificaitons then restarts the GUI and dbus_generator services
    Deactivate restores the original files then restarts the GUI and dbus_generator services
    Uninstall performs a deactivate then removes all files installed as part of this package
        Reinstall is used by boot code to reactivate the Forwarder following a Venus software update
        It can also be choosen manually to bypass the GUI prompts after deactivating the repeater

Additional setup functionality is provided when running from the host:
    Install copies files to the Venus device then Activates the Forwarder
    Copy just copy the files without furter actions

Installing from a non-unix host requires some additional steps since the setup script won't run there.
I suggest that the GitHub repository be downloaded as a zip file and copied to the Venus target.
From there, it can be unzipped and the entire directory structure moved to /data/GeneratorConnector.
Then, cd to that directory and run ./setup. This will help the install run smoothly.

A setup option is also provided to display the dbus_generator log without making any changes.


Setup command line interface:

setup has a command line interface that allows bypassing some or all of the user prompts.

Each parameter is optional and may occur in any order.
A missing parameter results in a user prompt for it's value.
The complete set is:

./setup [ip venusIp] [action]

ip provides the IP address of the veus device
This opiton is only meaningful if setup is running on the host and is ignored if running on the target.

action is one of the following:
    install or in or i (host only)
    copy or c (host only)
    activate or a
    deactivate or d
    uninstall or u
    log or l
    quit or q


CAUTION

The startstoy.py changes are substantial. The entire file is replaced during installation. The original file is moved to startstop.py.orig in the same directory.

Likewise, OverviewGenerator.qml is replaced and the original moved to OriginalGenerator.qml.orig

Future versions of Venus software may make changes to these files that would be lost when installing these changes. setup compares the stock files with a copy provided as part of the package. If they are the same, setup continues. If they differ, a prompt asking what to do appears. The setup may be allowed to continue or aborted.

Automatic reactivation following a Venus software update will succeed only if the stock files match the expected versions. Otherwise the reinstall will fail and a warning written to the dbus_generator log.


Examples:

./setup ip 192.168.3.162 install

copies files from the host then replaces affected files on 192.168.3.162

./setup uninstall

restores stock functionality and installed files

./setup ip 192.168.3.162 log

displays the last 100 lines of the Forwarder log from Venus device at 192.168.3.162

File organization:

All files associated with this package are placed in /data/GeneratorConnector. The setup script expects this location and may file if placed elsewhere.

Note: Unlike other parts of teh file system, /data survives a Venus software update.

/opt/victronenergy/gui/qml/OverviewGenerator.qml is replaced.
/opt/victronenergy/dbus-generator-starter/startstop.py is replaced.
The original files are moved to a file ending in .orig for easy uninstall in the future.


Debugging aids:

You can view logs using the unix 'tail' command:

GUI log:

tail /var/log/gui/current | tai64nlocal

dbus_generator log:

tail /var/log/dbus-generator-starter/current | tai64nlocal

tai64nlocal converts the timestamp at the beginning of each log entry to a human readable date and time (as UTC/GMT because Venus runs with system local time set to UTC).

dbus-spy can also be used to examine dBus services to aid in troubleshooting problems.

