---
title: Dashboard
type:
status:
canon_level:
---

# Dashboard

Незавершённые з
```dataview
TABLE type, canon_level, updated_at
FROM "Альгона"
WHERE status != "final"
SORT updated_at DESC
```
Незаполненные Обязательные поля:
```dataview
TABLE type, status, canon_level
FROM "Альгона"
WHERE !type OR !status
SORT file.name ASC
```

















