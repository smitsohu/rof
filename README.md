# rofairy
rofairy is a small proof-of-concept tool, to showcase how administrative tools that require write permission can be used seamlessly in a read-only root filesystem. Its primary purpose is to enable traditional package managers like `apt` to function when system directories like /boot or /usr are in a read-only state.

Unlike previous approaches, rofairy allows the read-only restriction to be lifted only as necessary and only for the wrapped application, while all other processes in the system continue to operate within a read-only environment. This approach circumvents [potential][2] [issues][1] that arise when read-write filesystems are remounted back as read-only.

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
 -s,  --sync               - sync all filesystems that
                             were remounted read-write
```

### Example: system update in Debian/Ubuntu
Prefix `apt` with `rofairy` and specify which directories should be writable and which directories should be read-only:

`sudo rofairy --readwrite /boot --readwrite /usr --readonly /usr/local apt upgrade`

## Installation
Just download the script and make it executable:

`curl -o rofairy https://raw.githubusercontent.com/smitsohu/rofairy/main/bin/rofairy`

## How does this work
In Linux, filesystems are equipped with two read-only attributes: one associated with the mount point and the other with the superblock. The mount point attribute determines whether file modifications are allowed within a specific mount point. Conversely, the superblock attribute is a system-wide setting that impacts all mount points across all namespaces.

To enable write access to a file, both the mount point and superblock read-only flags must be unset. rofairy clears the superblock flag as needed and does not restore it. This way read-only access is controlled solely at the mount point level, allowing files to be read-only in the current mount namespace while becoming writable in a child mount namespace.

### But I want to restore the read-only superblock
Easy. Run `sudo mount -o remount,ro <any filesystem mountpoint>` after you are done.
