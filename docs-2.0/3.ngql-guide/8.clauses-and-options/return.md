# RETURN

`RETURN`子句定义了 nGQL 查询的输出结果。如果需要返回多个字段，用英文逗号（,）分隔。

`RETURN`可以引导子句或语句：

- `RETURN`子句可以用于 nGQL 中的 openCypher 方式语句中，例如`MATCH`或`UNWIND`。

- `RETURN`可以单独使用，输出表达式的结果。

## openCypher 兼容性

本文操作仅适用于 nGQL 中的 openCypher 方式。关于原生 nGQL 如何定义输出结果，请参见 [`YIELD`](yield.md)。

`RETURN`不支持如下 openCypher 功能：

- 使用不在英文字母表中的字符作为变量名。例如：

  ```ngql
  MATCH (`点 1`:player) \
  RETURN `点 1`;
  ```

- 设置一个模式，并返回该模式匹配的所有元素。例如：

  ```ngql
  MATCH (v:player) \
  RETURN (v)-[e]->(v2);
  ```

## 历史版本兼容性

- 在 nGQL 1.x 中，`RETURN`适用于原生 nGQL，语法为`RETURN <var_ref> IF <var_ref> IS NOT NULL`。

- 从 nGQL 2.0 开始，`RETURN`不适用于原生 nGQL。

## Map 顺序说明

`RETURN`返回 Map 时，Key 的顺序是未定义的。

```ngql
nebula> RETURN {age: 32, name: "Marco Belinelli"};
+------------------------------------+
| {age:32,name:"Marco Belinelli"}    |
+------------------------------------+
| {age: 32, name: "Marco Belinelli"} |
+------------------------------------+

nebula> RETURN {zage: 32, name: "Marco Belinelli"};
+-------------------------------------+
| {zage:32,name:"Marco Belinelli"}    |
+-------------------------------------+
| {name: "Marco Belinelli", zage: 32} |
+-------------------------------------+
```

## 返回点或边

使用`RETURN {<vertex_name> | <edge_name>}`返回点或边的所有信息。

```ngql
// 返回点
nebula> MATCH (v:player) \
        RETURN v;
+---------------------------------------------------------------+
| v                                                             |
+---------------------------------------------------------------+
| ("player104" :player{age: 32, name: "Marco Belinelli"})       |
| ("player107" :player{age: 32, name: "Aron Baynes"})           |
| ("player116" :player{age: 34, name: "LeBron James"})          |
| ("player120" :player{age: 29, name: "James Harden"})          |
| ("player125" :player{age: 41, name: "Manu Ginobili"})         |
+---------------------------------------------------------------+
...

// 返回边
nebula> MATCH (v:player)-[e]->() \
        RETURN e;
+------------------------------------------------------------------------------+
| e                                                                            |
+------------------------------------------------------------------------------+
| [:follow "player104"->"player100" @0 {degree: 55}]                           |
| [:follow "player104"->"player101" @0 {degree: 50}]                           |
| [:follow "player104"->"player105" @0 {degree: 60}]                           |
| [:serve "player104"->"team200" @0 {end_year: 2009, start_year: 2007}]        |
| [:serve "player104"->"team208" @0 {end_year: 2016, start_year: 2015}]        |
+------------------------------------------------------------------------------+
...
```

## 返回点 ID

使用`id()`函数返回点 ID。
```ngql
nebula> MATCH (v:player{name:"Tim Duncan"}) \
        RETURN id(v);
+-------------+
| id(v)       |
+-------------+
| "player100" |
+-------------+
```

## 返回 Tag

使用`labels()`函数返回点上的 Tag 列表。

```ngql
nebula> MATCH (v:player{name:"Tim Duncan"}) \
        RETURN labels(v);
+------------+
| labels(v)  |
+------------+
| ["player"] |
+------------+
```

返回列表`labels(v)`中的第 N 个元素，可以使用`labels(v)[n-1]`。例如下面示例使用`labels(v)[0]`检索第一个元素。

```ngql
nebula> MATCH (v:player{name:"Tim Duncan"}) \
        RETURN labels(v)[0];
+--------------+
| labels(v)[0] |
+--------------+
| "player"     |
+--------------+
```

## 返回属性

使用语法`{<vertex_name>|<edge_name>}.<property>`返回点或边的属性。

```ngql
nebula> MATCH (v:player) \
        RETURN v.player.name, v.player.age \
        LIMIT 3;
+------------------+--------------+
| v.player.name    | v.player.age |
+------------------+--------------+
| "Danny Green"    | 31           |
| "Tiago Splitter" | 34           |
| "David West"     | 38           |
+------------------+--------------+
```

使用`properties()`函数返回点或边的所有属性。

```ngql
nebula> MATCH p=(v:player{name:"Tim Duncan"})-[]->(v2) \
        RETURN properties(v2);
+----------------------------------+
| properties(v2)                   |
+----------------------------------+
| {name: "Spurs"}                  |
| {age: 36, name: "Tony Parker"}   |
| {age: 41, name: "Manu Ginobili"} |
+----------------------------------+
```

## 返回 Edge type

使用`type()`函数返回匹配的 Edge type。

```ngql
nebula> MATCH p=(v:player{name:"Tim Duncan"})-[e]->() \
        RETURN DISTINCT type(e);
+----------+
| type(e)  |
+----------+
| "serve"  |
| "follow" |
+----------+
```

## 返回路径

使用`RETURN <path_name>`返回匹配路径的所有信息。

```ngql
nebula> MATCH p=(v:player{name:"Tim Duncan"})-[*3]->() \
        RETURN p;
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| p                                                                                                                                                                                                                                                                                                              |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:follow@0 {degree: 90}]->("player102" :player{age: 33, name: "LaMarcus Aldridge"})-[:serve@0 {end_year: 2019, start_year: 2015}]->("team204" :team{name: "Spurs"})>         |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:follow@0 {degree: 90}]->("player102" :player{age: 33, name: "LaMarcus Aldridge"})-[:serve@0 {end_year: 2015, start_year: 2006}]->("team203" :team{name: "Trail Blazers"})> |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:follow@0 {degree: 90}]->("player102" :player{age: 33, name: "LaMarcus Aldridge"})-[:follow@0 {degree: 75}]->("player101" :player{age: 36, name: "Tony Parker"})>           |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
...
```

### 返回路径中的点

使用`nodes()`函数返回路径中的所有点。

```ngql
nebula> MATCH p=(v:player{name:"Tim Duncan"})-[]->(v2) \
        RETURN nodes(p);
+-------------------------------------------------------------------------------------------------------------+
| nodes(p)                                                                                                    |
+-------------------------------------------------------------------------------------------------------------+
| [("player100" :player{age: 42, name: "Tim Duncan"}), ("team204" :team{name: "Spurs"})]                      |
| [("player100" :player{age: 42, name: "Tim Duncan"}), ("player101" :player{age: 36, name: "Tony Parker"})]   |
| [("player100" :player{age: 42, name: "Tim Duncan"}), ("player125" :player{age: 41, name: "Manu Ginobili"})] |
+-------------------------------------------------------------------------------------------------------------+
```

### 返回路径中的边

使用`relationships()`函数返回路径中的所有边。

```ngql
nebula> MATCH p=(v:player{name:"Tim Duncan"})-[]->(v2) \
        RETURN relationships(p);
+-------------------------------------------------------------------------+
| relationships(p)                                                        |
+-------------------------------------------------------------------------+
| [[:serve "player100"->"team204" @0 {end_year: 2016, start_year: 1997}]] |
| [[:follow "player100"->"player101" @0 {degree: 95}]]                    |
| [[:follow "player100"->"player125" @0 {degree: 95}]]                    |
+-------------------------------------------------------------------------+
```

### 返回路径长度

使用`length()`函数检索路径的长度。

```ngql
nebula> MATCH p=(v:player{name:"Tim Duncan"})-[*..2]->(v2) \
        RETURN p AS Paths, length(p) AS Length;
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------+
| Paths                                                                                                                                                                                                                  | Length |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------+
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:serve@0 {end_year: 2016, start_year: 1997}]->("team204" :team{name: "Spurs"})>                                                                                   | 1      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})>                                                                                     | 1      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player125" :player{age: 41, name: "Manu Ginobili"})>                                                                                   | 1      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:serve@0 {end_year: 2018, start_year: 1999}]->("team204" :team{name: "Spurs"})>     | 2      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:serve@0 {end_year: 2019, start_year: 2018}]->("team215" :team{name: "Hornets"})>   | 2      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:follow@0 {degree: 95}]->("player100" :player{age: 42, name: "Tim Duncan"})>        | 2      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:follow@0 {degree: 90}]->("player102" :player{age: 33, name: "LaMarcus Aldridge"})> | 2      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player101" :player{age: 36, name: "Tony Parker"})-[:follow@0 {degree: 95}]->("player125" :player{age: 41, name: "Manu Ginobili"})>     | 2      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player125" :player{age: 41, name: "Manu Ginobili"})-[:serve@0 {end_year: 2018, start_year: 2002}]->("team204" :team{name: "Spurs"})>   | 2      |
| <("player100" :player{age: 42, name: "Tim Duncan"})-[:follow@0 {degree: 95}]->("player125" :player{age: 41, name: "Manu Ginobili"})-[:follow@0 {degree: 90}]->("player100" :player{age: 42, name: "Tim Duncan"})>      | 2      |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------+
```

## 返回所有元素

使用星号（*）返回匹配模式中的所有元素。

```ngql
nebula> MATCH (v:player{name:"Tim Duncan"}) \
        RETURN *;
+----------------------------------------------------+
| v                                                  |
+----------------------------------------------------+
| ("player100" :player{age: 42, name: "Tim Duncan"}) |
+----------------------------------------------------+

nebula> MATCH (v:player{name:"Tim Duncan"})-[e]->(v2) \
        RETURN *;
+----------------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
| v                                                  | e                                                                     | v2                                                    |
+----------------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
| ("player100" :player{age: 42, name: "Tim Duncan"}) | [:follow "player100"->"player101" @0 {degree: 95}]                    | ("player101" :player{age: 36, name: "Tony Parker"})   |
| ("player100" :player{age: 42, name: "Tim Duncan"}) | [:follow "player100"->"player125" @0 {degree: 95}]                    | ("player125" :player{age: 41, name: "Manu Ginobili"}) |
| ("player100" :player{age: 42, name: "Tim Duncan"}) | [:serve "player100"->"team204" @0 {end_year: 2016, start_year: 1997}] | ("team204" :team{name: "Spurs"})                      |
+----------------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
```

## 重命名字段

使用语法`AS <alias>`重命名输出结果中的字段。

```ngql
nebula> MATCH (v:player{name:"Tim Duncan"})-[:serve]->(v2) \
        RETURN v2.team.name AS Team;
+---------+
| Team    |
+---------+
| "Spurs" |
+---------+

nebula> RETURN "Amber" AS Name;
+---------+
| Name    |
+---------+
| "Amber" |
+---------+
```

## 返回不存在的属性

如果匹配的结果中，某个属性不存在，会返回`NULL`。

```ngql
nebula> MATCH (v:player{name:"Tim Duncan"})-[e]->(v2) \
        RETURN v2.player.name, type(e), v2.player.age;
+-----------------+----------+---------------+
| v2.player.name  | type(e)  | v2.player.age |
+-----------------+----------+---------------+
| "Manu Ginobili" | "follow" | 41            |
| __NULL__        | "serve"  | __NULL__      |
| "Tony Parker"   | "follow" | 36            |
+-----------------+----------+---------------+
```

## 返回表达式结果

`RETURN`语句可以返回字面量、函数或谓词等表达式的结果。

```ngql
nebula> MATCH (v:player{name:"Tony Parker"})-->(v2:player) \
        RETURN DISTINCT v2.player.name, "Hello"+" graphs!", v2.player.age > 35;
+---------------------+----------------------+--------------------+
| v2.player.name      | ("Hello"+" graphs!") | (v2.player.age>35) |
+---------------------+----------------------+--------------------+
| "LaMarcus Aldridge" | "Hello graphs!"      | false              |
| "Tim Duncan"        | "Hello graphs!"      | true               |
| "Manu Ginobili"     | "Hello graphs!"      | true               |
+---------------------+----------------------+--------------------+

nebula> RETURN 1+1;
+-------+
| (1+1) |
+-------+
| 2     |
+-------+

nebula> RETURN 3 > 1;
+-------+
| (3>1) |
+-------+
| true  |
+-------+

nebula> RETURN 1+1, rand32(1, 5);
+-------+-------------+
| (1+1) | rand32(1,5) |
+-------+-------------+
| 2     | 1           |
+-------+-------------+
```

## 返回唯一字段

使用`DISTINCT`可以删除结果集中的重复字段。

```ngql
# 未使用 DISTINCT。
nebula> MATCH (v:player{name:"Tony Parker"})--(v2:player) \
        RETURN v2.player.name, v2.player.age;
+---------------------+--------+
| v2.name             | v2.age |
+---------------------+--------+
| "Tim Duncan"        | 42     |
| "LaMarcus Aldridge" | 33     |
| "Marco Belinelli"   | 32     |
| "Boris Diaw"        | 36     |
| "Dejounte Murray"   | 29     |
| "Tim Duncan"        | 42     |
| "LaMarcus Aldridge" | 33     |
| "Manu Ginobili"     | 41     |
+---------------------+--------+

# 使用 DISTINCT。
nebula> MATCH (v:player{name:"Tony Parker"})--(v2:player) \
        RETURN DISTINCT v2.player.name, v2.player.age;
+---------------------+--------+
| v2.name             | v2.age |
+---------------------+--------+
| "Tim Duncan"        | 42     |
| "LaMarcus Aldridge" | 33     |
| "Marco Belinelli"   | 32     |
| "Boris Diaw"        | 36     |
| "Dejounte Murray"   | 29     |
| "Manu Ginobili"     | 41     |
+---------------------+--------+
```
