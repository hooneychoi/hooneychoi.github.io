---
title: Family Tree
date: 2023-11-11 09:20 +0900
author: hoon
category:
- SQL
tags:
- CTE
---
```SQL
use [work]
go

CREATE TABLE parent_child
(
    id INT,
    first_name VARCHAR(512),
    last_name VARCHAR(512),
    parent_id INT
);

INSERT INTO parent_child
    (id, first_name, last_name, parent_id)
VALUES
    ('1', 'Rosa', 'Wellington', NULL),
    ('2', 'Jon', 'Wellington', '1'),
    ('3', 'Joni', 'Wellington', '1'),
    ('4', 'Marge', 'Wellington', '1'),
    ('5', 'Mary', 'Dijkstra', '2'),
    ('6', 'Frank', 'Wellington', '2'),
    ('7', 'Jason', 'Wellington', '3'),
    ('8', 'Bobby', 'Wellington', '4'),
    ('9', 'Sammy', 'Wellington', '4'),
    ('10', 'Sarah', 'Wellington', '4'),
    ('11', 'Sam Francis', 'Dijkstra', '5'),
    ('12', 'Stephen', 'Wellington', '6'),
    ('13', 'Trent', 'Wellington', '6'),
    ('14', 'June', 'Wellington', '9'),
    ('15', 'Josephine', 'Wellington', '9'),
    ('16', 'Suzy', 'Wellington', '9');

;

WITH generation
AS (
    SELECT id
        , first_name
        , last_name
        , parent_id
        , 0 AS generation_number
        , convert(varchar(8000),concat(first_name, ' ', last_name)) tree
    FROM parent_child
    WHERE parent_id IS NULL
    
    UNION ALL
    
    SELECT child.id
        , child.first_name
        , child.last_name
        , child.parent_id
        , generation_number + 1 AS generation_number
        , convert(varchar(8000),concat(tree,' > ',concat(child.first_name, ' ', child.last_name))) tree

    FROM parent_child child
    JOIN generation g
        ON g.id = child.parent_id
    )
SELECT first_name
    , last_name
    , generation_number
    , tree
    , concat(replicate('--',generation_number*3),first_name, ' ', last_name) tree2
FROM generation;
```

|first_name|last_name|generation_number|tree|tree2|
|---|---|---|---|---|
|Rosa|Wellington|0|Rosa Wellington|Rosa Wellington|
|Jon|Wellington|1|Rosa Wellington &gt; Jon Wellington|------Jon Wellington|
|Joni|Wellington|1|Rosa Wellington &gt; Joni Wellington|------Joni Wellington|
|Marge|Wellington|1|Rosa Wellington &gt; Marge Wellington|------Marge Wellington|
|Bobby|Wellington|2|Rosa Wellington &gt; Marge Wellington &gt; Bobby Wellington|------------Bobby Wellington|
|Sammy|Wellington|2|Rosa Wellington &gt; Marge Wellington &gt; Sammy Wellington|------------Sammy Wellington|
|Sarah|Wellington|2|Rosa Wellington &gt; Marge Wellington &gt; Sarah Wellington|------------Sarah Wellington|
|June|Wellington|3|Rosa Wellington &gt; Marge Wellington &gt; Sammy Wellington &gt; June Wellington|------------------June Wellington|
|Josephine|Wellington|3|Rosa Wellington &gt; Marge Wellington &gt; Sammy Wellington &gt; Josephine Wellington|------------------Josephine Wellington|
|Suzy|Wellington|3|Rosa Wellington &gt; Marge Wellington &gt; Sammy Wellington &gt; Suzy Wellington|------------------Suzy Wellington|
|Jason|Wellington|2|Rosa Wellington &gt; Joni Wellington &gt; Jason Wellington|------------Jason Wellington|
|Mary|Dijkstra|2|Rosa Wellington &gt; Jon Wellington &gt; Mary Dijkstra|------------Mary Dijkstra|
|Frank|Wellington|2|Rosa Wellington &gt; Jon Wellington &gt; Frank Wellington|------------Frank Wellington|
|Stephen|Wellington|3|Rosa Wellington &gt; Jon Wellington &gt; Frank Wellington &gt; Stephen Wellington|------------------Stephen Wellington|
|Trent|Wellington|3|Rosa Wellington &gt; Jon Wellington &gt; Frank Wellington &gt; Trent Wellington|------------------Trent Wellington|
|Sam Francis|Dijkstra|3|Rosa Wellington &gt; Jon Wellington &gt; Mary Dijkstra &gt; Sam Francis Dijkstra|------------------Sam Francis Dijkstra|


## 참조
- <https://learnsql.com/blog/query-parent-child-tree/>