Note: I've taken this, with thanks from @hartzell. All notes below are theirs:

# Setup

This describes getting a silver apple remote (A1294) working with a headless piCorePlayer.  It requires `v5.0.0`, which includes fixes for how `squeezelite` is called so that it finds the `~tc/.lircrc` file.

I'm using it with an IQaudio DAC+ with a 3-pin header soldered into the appropriate holes.

LIRC has a git repository, [lirc git repo][lirc-git], but unfortunately its web-based git browser isn't working.  If you clone the repo, you'll find a [configuration document][lirc-config] that's interesting reading.

The [LIRC project web site][lirc] includes a database of `lircd.conf` files for various remotes that includes entries for the silver and white apple remotes.  **BUT**, they're for an older version of LIRC and don't work with the piCorePlayer version.  I found functional versions on the net for the [A1294][lirc-a1294] and the [A1156][lirc-1156] (*untested).

[Squeezelite][squeezelite] uses the [lirc_client][lirc-client] API to interact with LIRC.  It compares events that it receives with a set of cmds from the "squeezeplayer" remote and then from a table populated from a `.lircrc` file. When piCorePlayer has LIRC installed and you've uploaded a custom `lircrc` file, squeezeplayer is run with the `-i /home/tc/.lircrc` option.

# Debugging

1. use `sudo mode2 -d /dev/lirc0` to see the raw events coming from LIRC.  This is the lowest-ish level and will confirm that you have the kernel bits in place and the hardware wired up correctly.
2. use `irw` to dump the higher level events (e.g. `KEY_OK`), which confirms that you have a `lircd.conf` file that works for your device.
3. enable ir debug (or sdebug?) output in the piCorePlayer squeezelite page and watch the log file (e.g. ssh into the pi at `tc` and `tail -f /var/log/pcp_squeezelite.log`) to see what squeezelite is doing with the events.

[lirc]: http://www.lirc.org/
[lirc-git]: http://www.lirc.org/git.html
[lirc-client]: http://www.lirc.org/html/lirc_client.html
[lirc-config]: file:///Users/hartzell/tmp/lirc/doc/html-source/configuration-guide.html
[squeezelite]: https://github.com/ralph-irving/squeezelite
[squeezelite-ir]: https://github.com/ralph-irving/squeezelite/blob/master/ir.c#L137-L180
[pcp-lirc]: https://github.com/ralph-irving/tcz-lirc

[lirc-a1294]: https://github.com/osmc/osmc/blob/master/package/remote-osmc/files/etc/lirc/apple-silver-A1294-lircd.conf
[lirc-a1156]: https://github.com/osmc/osmc/blob/master/package/remote-osmc/files/etc/lirc/apple-white-A1156-lircd.conf
