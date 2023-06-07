# rofairy
rofairy is a small proof-of-concept, to showcase how administrative tools that necessitate write permission can be used seamlessly in a read-only root filesystem.

Unlike previous approaches, rofairy allows the read-only restriction to be lifted only as necessary and only for the wrapped application, while all other processes in the system continue to operate within a read-only environment. This approach circumvents [potential][2] [issues][1] that arise when read-write filesystems are remounted back as read-only.

Notably, rofairy enables traditional package managers like `apt` to function when core system partitions like /boot or /usr are in a read-only state.

[1]: https://archive.is/49UnK
[2]: https://archive.is/0g5RJ

## Usage
```
rofairy [options] application [application arguments|options]

Modify read-only restrictions per application.

Options:
 -r,  --readonly PATH      - remount PATH read-only
 -ra, --readonly-all PATH  - remount PATH read-only,
                             including all submounts
 -w,  --readwrite PATH     - remount PATH read-write
 -wa, --readwrite-all PATH - remount PATH read-write,
                             including all submounts
 -f,  --file FILE          - load directives from FILE;
                             if path is relative
                             search FILE in /etc/rofairy.d
 -h,  --help               - display this help and exit
```

### Example: system update in Debian/Ubuntu
To update a Debian/Ubuntu system, prefix `apt` with `rofairy` and specify which directories should be writable and which directories should be read-only:

`sudo rofairy --readwrite /boot --readwrite /usr --readonly /usr/local apt upgrade`

## Installation
Just download the script and make it executable:

`curl -o rofairy https://raw.githubusercontent.com/smitsohu/rofairy/main/bin/rofairy`
