---
title: Dashboard
type:
status:
canon_level:
---

# Dashboard

Незавершённые заметки
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
WHERE !type OR !status OR (contains(file.frontmatter, "canon_level") AND !canon_level)
SORT file.name ASC
```

















