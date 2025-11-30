```js
option = {
  title: {
    text: "OpenWebText 数据集样本尺寸分布",
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
        { name: "[0, 4K)", value: 4945263 },
        { name: "[4K, 8K)", value: 2054010 },
        { name: "[8K, 16K)", value: 720582 },
        { name: "[16K, 32K)", value: 215966 },
        { name: "[32K, 64K)", value: 60977 },
        { name: "[64K, 128K)", value: 16971 },
        { name: "[128K, 256K)", value: 16971 },
        { name: "[256K, +♾️)", value: 0 },
      ],
    },
  ],
};
```
