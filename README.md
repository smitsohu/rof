# rof

rof is a small proof-of-concept, designed to demonstrate the seamless execution of administrative tasks that necessitate write access within a read-only root filesystem.

Unlike previous approaches, rof allows the read-only restriction to be lifted only as necessary and only for the wrapped application, while all other processes in the system continue to operate within the read-only environment. This approach enhances security and eliminates [potential][2] [issues][1] that arise when read-write filesystems are remounted back as read-only.

Notably, rof enables traditional package managers like `apt` to function when core system partitions like /boot or /usr are in a read-only state.

[1]: https://archive.is/49UnK
[2]: https://archive.is/0g5RJ

## Usage
```
rof [options] application [application arguments|options]

Modify read-only restrictions per application.

Options:
 -r, --readonly PATH  - remount PATH read-only
 -w, --readwrite PATH - remount PATH read-write
 -f, --file FILE      - load directives from FILE;
                        if path is relative
                        FILE is searched in /etc/rof.d
 -h, --help           - display this help and exit
```

### Example: system update in Debian/Ubuntu

In order to update your system, just prefix `apt` with `rof` and specify which directories should be writable and which directories should be read-only:

`sudo rof --readwrite /boot --readwrite /usr --readonly /usr/local apt upgrade`

## Installation
Just download the script and make it executable:

https://raw.githubusercontent.com/smitsohu/rof/main/bin/rof

## What does rof mean?

rof is the read-only fairy.
