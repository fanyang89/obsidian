```js
option = {
  title: {
    text: "ImageNET-1K 数据集样本尺寸分布",
    left: "center",
  },
  legend: {
    orient: "vertical",
    left: "left",
  },
  series: [
    {
      name: "Samples",
      type: "pie",
      label: { formatter: "{b} {d}%" },
      data: [
        { name: "[0, 4K)", value: 20664 },
        { name: "[4K, 8K)", value: 39805 },
        { name: "[8K, 16K)", value: 113199 },
        { name: "[16K, 32K)", value: 459867 },
        { name: "[32K, 64K)", value: 545792 },
        { name: "[64K, 128K)", value: 75808 },
        { name: "[128K, 256K)", value: 11989 },
        { name: "[256K, +♾️)", value: 8096 },
      ],
    },
  ],
};
```
