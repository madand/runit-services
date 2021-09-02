runit-services
==============

Collection of [runit](http://smarden.org/runit/) service scripts missing from
the ~~Arch~~ [Artix Linux](https://artixlinux.org/) repos.

Table of Contents
-----------------
1. [System (root) Services](#system-services)
2. [User (Session) Services](#user-services)
3. [Runit Tips & Tricks](#runit-tips-tricks)
4. [License](#license)

<a name="system-services"></a>
System (root) Services
----------------------

### backlight

Preserve the current display backlight level between reboots.

### colord

[colord](https://www.freedesktop.org/software/colord/) is a system service that
makes it easy to manage, install and generate color profiles to accurately color
manage input and output devices.

### psd

[Profile Sync Daemon](https://github.com/graysky2/profile-sync-daemon): symlinks
and syncs browser profile dirs to RAM thus reducing HDD/SDD calls and
speeding-up browsers.

Works only with PSD v5.x, since v6+ they went systemd-only way.

### thinkfan

[thinkfan](https://github.com/vmatare/thinkfan): the minimalist fan control
program.

<a name="user-services"></a>
User (Session) Services
-----------------------

The services in this section depend on user session (X11 or otherwise) to be
initialized to be run. Moreover, some services can form dependency chains
(e.g. pipewire). For dependency checking, scripts assume certain installation
path, namely `~/.runit/sv`. For more info see the section
[How to Use User Session Services](#how-to-user-svc).

### mpd

[Music Player Daemon](https://github.com/MusicPlayerDaemon/MPD): a daemon for
playing music of various formats.

Note that the script `mpd/run` depends on `pipewire-pulse` service (see below).
If you don't use PipeWire, you need to delete or comment out the line 
`sv check ...`  in `mpd/run` file.

### redshift

[Redshift](https://github.com/jonls/redshift) adjusts the color temperature of
your screen according to your surroundings. This may help your eyes hurt less if
you are working in front of the screen at night.

### safeeyes

[Safe Eyes](https://slgobinath.github.io/SafeEyes/) is a Free and Open
Source tool for Linux users to reduce and prevent repetitive strain injury
(RSI).

### syncthing

[Syncthing](https://github.com/syncthing/syncthing) is a continuous file
synchronization program.

### picom

[picom](https://github.com/yshui/picom) is a compositor for X, and a fork of
Compton.

### pipewire

[PipeWire](https://pipewire.org/) is a project that aims to greatly improve
handling of audio and video under Linux.

Note that the PipeWire services are supposed to be run with a user session.
Moreover, they form a startup dependency chain (`pipewire` ->
`pipewire-media-session` -> `pipewire-pulse`). So if you want to use
`pipewire-pulse`, make sure that the other two are also installed and enabled.

The `pipewire-*/run` scripts assume a certain directory structure for checking
dependency. If you store your local services under a different directory, you
need to modify the `sv check` line in `pipewire-media-session/run` and
`pipewire-pulse/run` accordingly.

See the next section for the details on how to install and activate the pipewire
services.

<a name="runit-tips-tricks"></a>
Runit Tips & Tricks
-------------------

<a name="how-to-user-svc"></a>
### How to Use User Session Services

``` shell
mkdir -p ~/.runit/{sv,runsvdir}
cp -r /path/to/cloned/services/pipewire{,-media-session,-pulse} ~/.runit/sv
# Enable the services
ln -s ~/.runit/sv/pipewire ~/.runit/runsvdir/pipewire
ln -s ~/.runit/sv/pipewire-media-session ~/.runit/runsvdir/pipewire-media-session
ln -s ~/.runit/sv/pipewire-pulse ~/.runit/runsvdir/pipewire-pulse
```

Finally, use `runsvdir` to start the services:
``` shell
runsvdir ~/.runit/runsvdir
```
In order to start the services automatically upon login, you may want to add
the previous command to your `~/.xinitrc` or create a corresponding `.desktop`
file in `~/.config/autostart/`.

### Properly restarting logger sub-service

When you edit the logger script (e.g. `colord/log/run`) or otherwise change
the logger configuration (e.g. change the value of the variable `LOGGING_ENABLE`
in `colord/conf`), you need to properly restart *both* the service and its
logger sub-service. This is because Runit uses a pipe between the main service
process and the logger, so merely doing `sv restart colord/log` wont do the
trick.

Runit's `exit` command kills both the service and its logger. Here is how to do
it with a system service:

``` shell
sv exit colord
# The following is redundant, if you use runsvdir(1) to manage the service tree.
sv up colord
```

The same with a user session service:

``` shell
sv exit ~/.runit/sv/mpd
# The following is redundant, if you use runsvdir(1) to manage the service tree.
sv up ~/.runit/sv/mpd
```

<a name="license"></a>
License
-------

[CC0](https://creativecommons.org/publicdomain/zero/1.0/) / Public Domain

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](https://creativecommons.org/publicdomain/zero/1.0/)
