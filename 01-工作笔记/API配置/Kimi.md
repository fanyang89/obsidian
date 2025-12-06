---
title: Kimi
tags: api-key
base_url: "https://api.moonshot.cn/v1"
key:
  - "sk-5ISpOSK2pt7RZAuRZxuzaImXt0Ud0NQ7RMp5S0ShfjdsTbca"
  - "sk-xFQZZtxGU4Ot8E3RL0MUOWKN3fwq9xsXgVcTM2Ly6W7jKJru"
models:
  - kimi-k2-0711-preview
---

## Kimi K2 as claude

```bash
export ANTHROPIC_AUTH_TOKEN=你的API密钥
export ANTHROPIC_BASE_URL=https://api.moonshot.cn/anthropic/
```

## 模型列表

```bash
curl -s https://api.moonshot.cn/v1/models -H "Authorization: Bearer sk-5ISpOSK2pt7RZAuRZxuzaImXt0Ud0NQ7RMp5S0ShfjdsTbca"
```

```json
{
  "object": "list",
  "data": [
    {
      "created": 1754034606,
      "id": "moonshot-v1-128k",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "moonshot-v1-8k-vision-preview",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "moonshot-v1-32k",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "moonshot-v1-32k-vision-preview",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "kimi-k2-turbo-preview",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "kimi-thinking-preview",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "moonshot-v1-auto",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "kimi-k2-0711-preview",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "moonshot-v1-8k",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "moonshot-v1-128k-vision-preview",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    },
    {
      "created": 1754034606,
      "id": "kimi-latest",
      "object": "model",
      "owned_by": "moonshot",
      "permission": [
        {
          "created": 0,
          "id": "",
          "object": "",
          "organization": "moonshot",
          "group": "moonshot",
          "is_blocking": false
        }
      ],
      "root": "",
      "parent": ""
    }
  ]
}
```
