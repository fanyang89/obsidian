https://github.com/c-rainstorm/blog/blob/master/os/FileSystem-Ext4.md

## vfs

尽管Linux内核是C语言写的，但VFS是一种面向对象的框架，把文件相关的东西分为4个对象：

- Superblock: 一个文件系统有一个，含有文件系统的属性和接口
  - 属性：文件系统的一些参数；
  - 接口：mount 和 umount 接口等。
- Inode：一个文件有一个，含有文件的属性和**文件属性**的接口（不是文件读写的接口）。文件夹也当成文件处理，有自己的 inode。
  - 属性：文件名，创建时间，修改时间，访问权限，文件保存的 LBA 等；
  - 接口：创建，删除文件夹等。
- Dentry ：一个目录有一个，用来方便目录查找等。访问文件的时候，用户发下来文件路径，VFS 通过 Hash 的方法直接通过路径查到最终 Dentry，找到 inode，来直接查找一个路径，而不是一级级翻下来
  - 属性：目录名等；
  - 接口：查找文件路径等。
- File ：对文件进行操作的接口，这个是大家最熟悉的了，读写都通过它 - 属性：文件锁，当前访问的偏移地址等；- 接口：fopen，fclose，fwrite，fread，fsync，异步读写等。
  这些对象平时保存在磁盘上，使用时加载到内存并给各种属性和接口赋值，同时 inode 和 dentry 都是有缓存的，这样每次查找一个路径，就不用等啊等啊读磁盘了，翻一下缓存就能快速找到。

有人要问，真的靠一个 VFS 能统一所有的文件系统操作吗？微软那么配合啊，会采用和 Linux 一样的接口？所以 VFS 要求文件系统要有 Unix 风格，主要是三种 Style：

- 文件夹和文件都是一样的处理；
- 文件的信息和文件数据分开，文件信息放在inode里面，数据放在数据块里面；
- Superblock 来表示文件系统的信息。

Ex4 文件系统将磁盘分为一系列块组 (block groups)。为了减少磁盘碎片带来的性能问题，减少寻道时间，块分配器总是尝试将每个文件的所有块分配到同一个块组中。块组的大小由超级块中的属性来定义，通常是 `8 * block_size_in_bytes` 对于块大小为 4Kb 的磁盘来说，每一个组可以包含 32768 个 block，总共 128Mb。块组的数目为磁盘空间除以块组大小
每个块组的磁盘布局都基本相同，下面就块组 0 做简单介绍 - 1024 bytes 的 Group 0 Padding（boot block）只有 块组0 有，用于装载该分区的操作系统 - 超级块(super block)包含文件系统的所有关键参数。- Group Descriptors。用于记录块组内部相关的信息。- Reserved GDT Blocks。用于文件系统未来的拓展 - Data Block Bitmap。用于记录块组内部数据块的使用情况。- inode Bitmap。用于记录 inode table 中的 inode 的使用情况。- inode table - Data Block

一个块由偶数个扇区 (sector) 组成，一个块组又由多个块组成。块大小在 build file system 时被指定，通常是 4Kb。如果块大小大于内存页的大小，装载文件系统时可能会遇到问题。

![](assets/Pasted%20image%2020221116165618.png)
