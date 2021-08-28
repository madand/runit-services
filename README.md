runit-services
==============

Collection of [runit](http://smarden.org/runit/) service scripts missing from
the Arch Linux repos.

Services
--------

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

### mpd

[Music Player Daemon](https://github.com/MusicPlayerDaemon/MPD): a daemon for
playing music of various formats.

mpd service is supposed to be run with a user session. For more info on
how to set up user services see [Installation as local services](#installation-as-local-services) 
in `pipewire` service notes.

### syncthing

[Syncthing](https://github.com/syncthing/syncthing) is a continuous file
synchronization program.

Syncthing service is supposed to be run with a user session. For more info on
how to set up user services see [Installation as local services](#installation-as-local-services) 
in `pipewire` service notes.

### picom

[picom](https://github.com/yshui/picom) is a compositor for X, and a fork of
Compton.

picom service is supposed to be run with a user session. For more info on
how to set up user services see [Installation as local services](#installation-as-local-services) 
in `pipewire` service notes.

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

#### Installation as local services

``` shell
mkdir -p ~/.runit/{sv,runsvdir}
cp -r /path/to/cloned/services/pipewire{,-media-session,-pulse} ~/.runit/sv
# Enable the services
cd ~/.runit/runsvdir
ln -s ../pipewire
ln -s ../pipewire-media-session
ln -s ../pipewire-pulse
```

Finally, use `runsvdir` to start the services:
``` shell
runsvdir ~/.runit/runsvdir
```
In order to start the services automatically upon login, you may want to add
the previous command to your `~/.xinitrc` or create a corresponding `.desktop`
file in `~/.config/autostart/`.


License
-------

[CC0](https://creativecommons.org/publicdomain/zero/1.0/) / Public Domain

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](https://creativecommons.org/publicdomain/zero/1.0/)
