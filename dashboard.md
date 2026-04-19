---
title: Dashboard
created: 2026-04-19
updated: 2026-04-19
type: dashboard
tags: [meta]
sources: []
---

# Dashboard

> Landing page for this wiki. Auto-generated stats and views via Dataview.
> **Requires the [Dataview plugin](https://blacksmithgu.github.io/obsidian-dataview/).**
> If Dataview isn't installed, the code blocks below will show as literal text.

## At a Glance

```dataview
TABLE WITHOUT ID
  length(rows) AS "Pages"
FROM ""
WHERE type AND type != "log" AND type != "index" AND type != "dashboard"
GROUP BY true
```

## Pages by Type

```dataview
TABLE WITHOUT ID
  type AS "Type",
  length(rows) AS "Count"
FROM ""
WHERE type AND type != "log" AND type != "index" AND type != "dashboard"
GROUP BY type
SORT length(rows) DESC
```

## Recent Ingests

```dataview
TABLE
  updated AS "Updated",
  length(sources) AS "Sources"
FROM ""
WHERE type = "source-summary" OR type = "paper-summary" OR type = "book-summary"
SORT updated DESC
LIMIT 10
```

## Recently Updated Pages

```dataview
TABLE
  type AS "Type",
  updated AS "Updated"
FROM ""
WHERE type AND type != "log" AND type != "index" AND type != "dashboard"
SORT updated DESC
LIMIT 10
```

## Needs Attention

### Orphan pages (zero inbound links)

```dataview
LIST
FROM ""
WHERE type
  AND length(file.inlinks) = 0
  AND type != "index" AND type != "log" AND type != "dashboard"
SORT file.name ASC
LIMIT 20
```

### Pages with unresolved contradictions

```dataview
TABLE contradictions
FROM ""
WHERE contradictions AND length(contradictions) > 0
SORT updated DESC
```

### Stale pages (not updated in 90+ days)

```dataview
TABLE updated
FROM ""
WHERE type
  AND type != "log" AND type != "index" AND type != "dashboard"
  AND (date(today) - date(updated)).days > 90
SORT updated ASC
LIMIT 20
```

### Pages missing sources

```dataview
LIST
FROM ""
WHERE type
  AND type != "index" AND type != "log" AND type != "dashboard"
  AND (!sources OR length(sources) = 0)
SORT file.name ASC
LIMIT 20
```

## Most Connected

### Entities by inbound links

```dataview
TABLE length(file.inlinks) AS "Referenced by"
FROM ""
WHERE type = "entity"
SORT length(file.inlinks) DESC
LIMIT 10
```

### Concepts by inbound links

```dataview
TABLE length(file.inlinks) AS "Referenced by"
FROM ""
WHERE type = "concept"
SORT length(file.inlinks) DESC
LIMIT 10
```

## Tag Cloud

```dataview
TABLE WITHOUT ID
  tag AS "Tag",
  length(rows) AS "Count"
FROM ""
FLATTEN tags AS tag
WHERE tag
GROUP BY tag
SORT length(rows) DESC
LIMIT 20
```

## Navigation

- [[index|Index]] — full content catalog
- [[log|Log]] — chronological action log
- [[SCHEMA|Schema]] — conventions and rules for this wiki
