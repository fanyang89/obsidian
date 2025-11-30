## 布局设计

- `{bucket}/blob-{hash_prefix}.{file_ext}` 数据
- `{bucket}/mblob-{hash_prefix}` 元数据
- `{bucket}/tblob-{hash_prefix}` 缩略图（如果是图片）
- `{bucket}/vblob-{hash_prefix}` 向量
  需要使用 `HeadObject` 判断 `BLOB-{hash}` 是否存在，从 1 位开始；如果存在，则增长位数。

## 元数据

格式使用 `JSON`

- 标签
- 格式
- 文件名
- 摘要
- 大小
- 最后更新

## 管理

- 定期遍历所有的元数据 metadata，构建 Markdown table 到 obsidian 中
- 可能可以去掉 NocoDB？
- 是否需要接入 AI 实现自动 Tag 和摘要
- 是否需要语义化搜索？

## 命名

`r2b`：R2 x Blob
