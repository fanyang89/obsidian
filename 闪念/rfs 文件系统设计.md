---
title: rfs 文件系统设计
---

# rfs 文件系统设计

#文件系统

`webdav.FileSystem` 需要的接口

- `Mkdir`：创建文件夹
- `OpenFile`：打开文件/文件夹
- `RemoveAll`：删除
- `Rename`：重命名
- `Stat`：返回文件的信息

`webdav.File` 需要的接口：

- `Read`
- `Write`
- `Seek`
- `Stat`
- `Readdir`
- `Close`

使用一个 key-value DB 记录文件的元数据。

文件系统的实际结构：

```bash
/ base dir
  / metadb <- pebble db
  / files
	  - cluster 1
	    * File A
	    * File B
	  - cluster 2  <-- after snapshot
	    * File A
```

因此，需要记录每个文件，位于哪一个 `fcluster`。还需要记录这个文件是否是 `cow`。如果 `cow=1`，说明文件只读，并且需要在写的时候，进行复制，然后更新这个文件位于 `fcluster 2`。

文件就是文件夹。

创建文件：

```bash
rfs$<path> -> FileInfo { atime, ..., cluster, is_dir }
rfs$meta$cluster$latest -> 2
rfs$meta$raft$index
```

`Readdir` 例子：

```bash
rfs$a$dir         <- dir
rfs$a/foo1$file   <- file
rfs$a/foo2$file   <- file
rfs$a/b$dir       <- dir
rfs$a/b/foo1$file <- file
rfs$a/b/foo2$file <- file
```
