```base
filters:
  and:
    - file.hasTag("example")
views:
  - type: table
    name: Table
    sort:
      - property: file.name
        direction: ASC
```
