+++
title = "介绍 org-ql Queries 语法"
description = "博客简介"
date = 2023-05-28T18:43:00+08:00
lastmod = 2023-05-28T18:43:50+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

`org-ql` 查询是一个 lisp 表达式，它可以包含任意表达式，也可以调用某些内置谓词 predicates 。 `org-ql` 被编译成一个谓词函数，该函数在 org 缓冲区中的每个标题上进行点测试;当 non-nil 时，返回匹配到的标题。在可能的情况下，某些内置谓词被优化为全缓冲区正则表达式搜索，这比在每个标题上测试谓词要快得多。 

**Notes:** 

-   像 `"string"` 这样的空字符串会自动转换为 `(regexp "string")` 谓词。
-   标准数值比较函数符号(`<~、~<=~、~>~、~>=~、~=`)作为参数传递给接受它们的谓词时不需要加引号。与中缀表示法的相似是巧合。


## Non-sexp query syntax {#non-sexp-query-syntax}

`org-ql-search` 命令也接受另一种 `non-sexp` 查询语法，而 `helm-org-ql` 命令只接受这种语法。语法很简单，两种语法中的几个查询示例就足够了。默认情况下，当使用多个谓词时，它们与布尔值 `and` 组合在一起。 

| Sexp syntax                                       | Non-sexp syntax                                  |
|---------------------------------------------------|--------------------------------------------------|
| `(todo)`                                          | `todo:`                                          |
| `(todo "SOMEDAY")`                                | `todo:SOMEDAY`                                   |
| `(todo "SOMEDAY" "WAITING")`                      | `todo:SOMEDAY,WAITING`                           |
| `(ts :on today)`                                  | `ts:on=today`                                    |
| `(ts-active :from "2017-01-01" :to "2018-01-01")` | `ts-active:from=2017-01-01,to=2018-01-01`        |
| `(clocked :on -1)`                                | `clocked:on=-1`                                  |
| `(heading "quoted phrase" "word")`                | `heading:"quoted phrase",word`                   |
| `(and (tags "book" "books") (priority "A"))`      | `tags:book,books priority:A`                     |
| `(src :lang "elisp" :regexps ("defun"))`          | `src:defun,lang=elisp` or `src:lang=elisp,defun` |
| `(and (tags "space") (not (regexp "moon")))`      | `tags:space !moon`                               |
| `(priority >= B)`                                 | `priority:A,B`                                   |

请注意: `effort` 、 `level` 和 `priority` 谓词在 `non-sexp` 语法中不支持比较器，因此应该传递多个参数，如最后一个示例所示。 


## General predicates {#general-predicates}

在适用的情况下， `参数列` 在 谓词名称 旁边。 

`blocked`
: 如果当前标题被阻塞，返回 non-nil。调用 `org-entry-blocked-p` ，见。

`category (&optional categories)`
: 如果当前标题在一个或多个 `CATEGORIES` 字符串列表中，则返回 non-nil。

`done`
: 如果条目的 `TODO` 关键字在 `org-done-keywords` 中，则返回 non-nil。

`effort (&optional effort-or-comparator effort)`
: 如果当前标题的 `effort` 属性与参数匹配，则返回非nil。可以接受以下形式: 
    1.  `(effort DURATION)` :如果effort为 `DURATION` ，则匹配。
    2.  `(effort DURATION DURATION)`:如果 effort 在是在DURATIONs之间，那就匹配，包括。
    3.  `(effort COMPARATOR DURATION)`:如果将 `DURATION` 与 `COMPARATOR` 进行比较，则匹配。~COMPARATOR~ may be `<`, `<=`, `>`, or `>=`. `DURATION` 是一个 org effort 字符串, like `5` or `0:05`.

`habit`
: 如果条目是 habit，则返 non-nil。

`heading (&rest strings)`
: 如果当前条目的标题全部匹配 `STRINGS` ，返回 non-nil。匹配不区分大小写。 
    -   别名: `h`.

`heading-regexp (&rest regexps)`
: 如果当前条目的标题匹配所有 `REGEXPS` (正则表达式字符串)，则返回 non-nil。匹配不区分大小写。 
    -   别名: `h*`.

`level (level-or-comparator &optional level)`
: 如果当前标题的大纲级别与参数匹配，则返回 non-nil 。可以接受以下形式: 
    1.  `(level NUMBER)` :如果标题级别为 `NUMBER` 则匹配。
    2.  `(level NUMBER NUMBER)`:如果标题级别等于或在 `NUMBER` 之间，则匹配。
    3.  `(level COMPARATOR NUMBER)`:如果标题级别与 `NUMBER` 和 `COMPARATOR` 比较，则匹配。 `COMPARATOR` 可以为 `<`, `<=`, `>`, or `>=` 。

`link (&optional description-or-target &key description target regexp-p)`
: Return non-nil if current heading contains a link matching arguments. `DESCRIPTION-OR-TARGET` is matched against the link's description and target.  Alternatively, one or both of `DESCRIPTION` and `TARGET` may be matched separately.  Without arguments, return non-nil if any link is found.

`outline-path (&rest strings)`
: 如果当前节点的大纲路径匹配所有的 `STRINGS` ，则返回 non-nil。每个字符串都可以作为子字符串出现在节点大纲路径的任何部分。例如: `Food/Fruits/Grapes` 将匹配 `(olp "Fruit" "Grape")` 。 
    -   别名: `olp`.

`outline-path-segment (&rest strings)`
: Return non-nil if current node's outline path matches `STRINGS`.  Matches `STRINGS` as a contiguous segment of the outline path.  Each string is compared as a substring.  For example the path `Food/Fruits/Grapes` would match `(olps "Fruit" "Grape")` but not `(olps "Food" "Grape")`. 
    -   Aliases: `olps`.

`path (&rest regexps)`
: Return non-nil if current heading's buffer's filename path matches any of `REGEXPS` (regexp strings).  Without arguments, return non-nil if buffer is file-backed.

`priority (&rest args)`
: Return non-nil if current heading has a certain priority.  `ARGS` may be either a list of one or more priority letters as strings, or a comparator function symbol followed by a priority letter string.  For example:  `(priority "A") (priority "A" "B") (priority '>= "B")` Note that items without a priority cookie never match this predicate (while Org itself considers items without a cookie to have the default priority, which, by default, is equal to priority `B`).

`property (property &optional value &key inherit)`
: Return non-nil if current entry has `PROPERTY` (a string), and optionally `VALUE` (a string).  If `INHERIT` is nil, only match entries with `PROPERTY` set on the entry; if t, also match entries with inheritance.  If `INHERIT` is not specified, use the Boolean value of `org-use-property-inheritance`, which see (i.e. it is only interpreted as nil or non-nil).

`regexp (&rest regexps)`
: Return non-nil if current entry matches all of `REGEXPS` (regexp strings).  Matches against entire entry, from beginning of its heading to the next heading. 
    -   Aliases: `r`.

`rifle (&rest strings)`
: Return non-nil if each string is found in either the entry or its outline path.  Works like `org-rifle`.  This is probably the most useful, intuitive, general-purpose predicate. 
    -   Aliases: `smart`.
    -   **Note:** By default, this is the default predicate used for plain-string query tokens (i.e. given without a specified predicate).  This can be customized with the option `org-ql-default-predicate`.

`src (&key lang regexps)`
: Return non-nil if current entry contains an Org Babel source block.  If `LANG` is non-nil, match blocks of that language.  If `REGEXPS` is non-nil, require that block's contents match all regexps.  Matching is done case-insensitively.

`tags (&optional tags)`
: 如果当前标题包含一个或多个 `TAGS` (字符串列表)，则返回非 nil 。测试继承标记和本地标记。

`tags-inherited (&optional tags)`
: 如果当前标题的继承标签包含一个或多个 `TAGS` (字符串列表)，则返回非 nil。如果 `TAGS` 为 nil，如果 heading 有任何继承的标签，则返回非 nil。 
    -   别名: `inherited-tags`, `tags-i`, `itags`.

`tags-local (&optional tags)`
: 如果当前标题的 local 标签包含一个或多个 `TAGS` (字符串列表)，则返回非 nil。如果 `TAGS` 为 nil，如果标题有任何 local 局部标记，则返回非 nil。 
    -   别名: `local-tags`, `tags-l`, `ltags`.

`tags-all (tags)`
: Return non-nil if current heading includes all of `TAGS`.  Tests both inherited and local tags. 
    -   Aliases: `tags&`.

`tags-regexp (&rest regexps)`
: 如果当前标题包含匹配一个或多个正则 `REGEXPS` 标签，则返回非 nil。测试继承标记和本地标记。 
    -   别名: `tags*`.

`todo (&optional keywords)`
: 如果当前标题是一个 `TODO` 项，则返回非 nil。对于 `KEYWORDS` ，如果关键字是 `KEYWORDS` (字符串列表)之一，则返回非 nil。当不带参数调用时，只匹配未完成的任务(即不匹配 `org-done-keywords` 中的关键字)。


## Ancestor/descendant predicates {#ancestor-descendant-predicates}

`ancestors (&optional query)`
: 如果当前标题有父标题，则返回 non-nil。如果 `QUERY` ，如果父标题匹配，则返回 non-nil。此选择器支持嵌套。

`children (&optional query)`
: 如果当前标题有直接子标题，则返回 non-nil。如果 `QUERY` ，如果子标题匹配，则返回 non-nil。这个选择器可以是嵌套的，例如匹配孙子标题。

`descendants (&optional query)`
: 如果当前标题有后代标题，则返回 non-nil。  If `QUERY`, 如果子标题匹配，则返回 non-nil。 这个选择器支持嵌套(如果你能找到嵌套!)

`parent (&optional query)`
: 如果当前标题有直接父标题，则返回 non-nil。如果 `QUERY` ，如果父标题匹配，则返回 non-nil。这个选择器可以是嵌套的，比如匹配父标题。


## Date/time predicates {#date-time-predicates}

这些谓词接受可选的关键字参数: 

-   `:from`: 在时间戳 `:from` 上或之后匹配的条目
-   `:to`: 在时间戳 `:to` 上或之前匹配的条目.
-   `:on`: 在此日期时间戳 `:on` 匹配的条目
-   `:with-time`: 如果未指定，匹配带或不带时间的时间戳(例如 `hh:mm` )。如果为 nil，匹配不带时间的时间戳。如果是，将时间戳与时间匹配。

时间戳/日期参数应该是天数(向前看是正数，向后看是负数)，可以通过 `parse-time-string` 解析的字符串(字符串可以省略时间值)，符号 `today` 或 `ts` 结构体。 

-   **Predicates** 
    -   **`ts`:** 如果当前条目具有给定时间段内的时间戳，则返回 non-nil。不带参数,如果条目有时间戳，则返回非空值。
    
    -   **`ts-active`, `ts-a`:** Like `ts`, 但只匹配活动时间戳。
    -   **`ts-inactive`, `ts-i`:** Like `ts`,但只匹配非活动时间戳。

除了关键字参数之外，下面的谓词也可以接受单个参数，即一个数字，它向后或向前看几天。这个数可以是负数来反转方向。 

这两个谓词解释单个数字参数，就好像它被传递给了 `:from` 关键字参数，这简化了搜索过去几天内计时或关闭的项目的常见情况: 

-   **Backward-looking** 
    -   **`clocked`:** Return non-nil if current entry was clocked in given period.  Without arguments, return non-nil if entry was ever clocked.  Note: Clock entries are expected to be clocked out.  Currently clocked entries (i.e. with unclosed timestamp ranges) are ignored.
    -   **`closed`:** Return non-nil if current entry was closed in given period.  Without arguments, return non-nil if entry is closed.

These predicates interpret a single number argument as if it were passed to the `:to` keyword argument, which eases the common case of searching for items planned in the next few days: 

-   **Forward-looking** 
    -   **`deadline`:** Return non-nil if current entry has deadline in given period.  If argument is `auto`, return non-nil if entry has deadline within `org-deadline-warning-days`.  Without arguments, return non-nil if entry has any deadline.
    -   **`planning`:** Return non-nil if current entry has planning timestamp (i.e. its deadline, scheduled, or closed timestamp) in given period.  Without arguments, return non-nil if entry has any planning timestamp.
    -   **`scheduled`:** Return non-nil if current entry is scheduled in given period.  Without arguments, return non-nil if entry is scheduled.

