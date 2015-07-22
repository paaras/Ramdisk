Ramdisk
=================

INTRODUCTION
------------
In this project I wrote a Linux kernel module that implemented a ramdisk: an in-memory block device. (A block device is basically a device that acts like a disk, supporting random-access read and write operations of fixed-sized units called sectors. Random-access means the disk can read or write sectors in any order -- unlike a pipe, for example.)

The ramdisk supports the usual read and write operations. You can read and write to the ramdisk by reading and writing a file, for instance one named /dev/osprda. The ramdisk also supports a special read/write locking feature, where a process can gain exclusive access to the ramdisk. This, of course, leads to some interesting synchronization issues.

Like most device drivers, the module can support several of these devices simultaneously.

Locking
-------------------
A write lock guarantees exclusive access to the ramdisk -- no other readers or writers have access to the ramdisk.

* A request for a read lock on a ramdisk file will block until no other files on that ramdisk have a write lock.
* A request for a write lock on a ramdisk file will block until no other files on that ramdisk have a read or write lock.
* Lock requests are served in the order in which they are received. Thus, for example, if P1 requests the write lock, and then P2 requests the read lock for the same ramdisk, then P1's request is served first and P2's request will be blocked until P1 has released the write lock.
