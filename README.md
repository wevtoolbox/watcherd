# watcherd

![tag](https://img.shields.io/github/v/tag/devilbox/watcherd.svg?colorB=orange&sort=semver)
[![linting](https://github.com/devilbox/watcherd/workflows/linting/badge.svg)](https://github.com/devilbox/watcherd/actions/workflows/linting.yml)
[![test-linux](https://github.com/devilbox/watcherd/workflows/test-linux/badge.svg)](https://github.com/devilbox/watcherd/actions/workflows/test-linux.yml)
[![test-macos](https://github.com/devilbox/watcherd/workflows/test-macos/badge.svg)](https://github.com/devilbox/watcherd/actions/workflows/test-macos.yml)
[![test-windows](https://github.com/devilbox/watcherd/workflows/test-windows/badge.svg)](https://github.com/devilbox/watcherd/actions/workflows/test-windows.yml)
[![License](https://img.shields.io/badge/license-MIT-%233DA639.svg)](https://opensource.org/licenses/MIT)


**[watcherd](https://github.com/devilbox/watcherd/blob/master/bin/watcherd)** will look for directory changes (added and deleted directories) under the specified path (`-p`) and will execute specified commands or shell scripts (`-a`, `-d`) depending on the event.
Once all events have happened during one round (`-i`), a trigger command can be executed (`-t`).
Note, the trigger command will only be execute when at least one add or delete command has succeeded with exit code 0.

---

If you need the same functionality to monitor changes of listening ports, check out **[watcherp](https://github.com/devilbox/watcherp)**.

---

### Modes

**[watcherd](https://github.com/devilbox/watcherd/blob/master/bin/watcherd)** can either use the native [inotifywait](https://linux.die.net/man/1/inotifywait) implementation or if this is not available on your system use a custom bash implementation. The default is to use bash.

### Placeholders

There are two placeholders available that make it easier to use custom commands/scripts for the add (`-a`) or delete (`-d`) action.:

* `%p` Full path to the directory that was added or deletd
* `%n` Name of the directory that was added or deleted

You can specify the placeholders as many times as you want. See the following example section for usage.

### Examples

By using **[vhost-gen](https://github.com/devilbox/vhost-gen)** (which is capable of creating Nginx or Apache vhost config files for normal vhosts or reverse proxies), the following will be able to create new nginx vhosts on-the-fly, simply by adding or deleting folders in your main www directory. The trigger command will simply force nginx to reload its configuration after directory changes occured.

```shell
# %n will be replaced by watcherd with the new directory name
# %p will be replaced by watcherd with the new directory path
watcherd -v \
  -p /shared/httpd \
  -a "vhost-gen -p %p -n %n -s" \
  -d "rm /etc/nginx/conf.d/%n.conf" \
  -t "nginx -s reload"
```

### Usage

```bash
Usage: watcherd -p <path> -a <cmd> -d <cmd> [-t <cmd> -w <str> -i <int> -v -c]
       watcherd --help
       watcherd --version

watcherd will look for directory changes (added and deleted directories) under
the specified path (-p) and will execute specified commands or shell scripts
(-a, -d) depending on the event. Once all events have happened during one round
(-i), a trigger command can be executed (-t). Note, the trigger command will
only be execute when at least one add or delete command has succeeded with exit
code 0.

Required arguments:
  -p <path>     Path to directoy to watch for changes.
  -a <cmd>      Command to execute when a directory was added.
                You can also append the following placeholders to your command string:
                %p The full path of the directory that changed (added, deleted).
                %n The name of the directory that changed (added, deleted).
                Example: -a "script.sh -f %p -c %n -a %p"
  -d <cmd>      Command to execute when a directory was deletd.
                You can also append the following placeholders to your command string:
                %p The full path of the directory that changed (added, deleted).
                %n The name of the directory that changed (added, deleted).
                Example: -d "script.sh -f %p -c %n -a %p"

Optional arguments:
  -e <regex>    Exclude regex for directories to ignore.
                E.g.: -e '\.*' to ignore dot directories.
  -t <cmd>      Command to execute after all directories have been added or
                deleted during one round.
                No argument will be appended.
  -w <str>      The directory watcher to use. Valid values are:
                'inotify': Uses inotifywait to watch for directory changes.
                'bash':    Uses a bash loop to watch for directory changes.
                The default is to use 'bash' as the watcher.
  -i <int>      When using the bash watcher, specify the interval in seconds
                for how often to look for directory changes.
  -v            Verbose output.
  -c            Colorized log output.

Misc arguments:
  --help        Show this help screen.
  --version     Show version information.
```

## 📄 License

**[MIT License](LICENSE.md)**

Copyright (c) 2017 **[cytopia](https://github.com/cytopia)**

Copyright (c) 2024 **[bogny](https://github.com/bogny)**
