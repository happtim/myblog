+++
date = "2023-11-02"
title = "DateTime UTC 格式"

categories = ['dotnet']
+++

## Introduction

ISO 8601是一个国际标准，用于表示日期和时间的格式。它由国际标准化组织（ISO）制定，
ISO 8601描述了大量的日期/时间格式以满足各种需求。
## Formats

不同的标准可能需要不同级别的日期和时间精确度，因此该配置文件定义了六个级别。引用此配置文件的标准应该指定其中一个或多个精确度。如果某个标准允许多个精确度，那么它应该指定具有降低精确度的日期和时间的含义，例如，比较具有不同精确度的两个日期的结果。

格式如下。必须准确地包含此处显示的组件，并且必须使用准确的标点符号。请注意，字符串中的"T"字面上出现，以指示时间元素的开始，正如ISO 8601中所指定的。

年：
YYYY (eg 1997)
年和月：
YYYY-MM (eg 1997-07)
完整日期格式：
YYYY-MM-DD (eg 1997-07-16)
完整的日期加上小时和分钟：
YYYY-MM-DDThh:mmTZD (eg 1997-07-16T19:20+01:00)
完整的日期加上小时、分钟和秒：
YYYY-MM-DDThh:mm:ssTZD (eg 1997-07-16T19:20:30+01:00)
完整的日期加上小时、分钟、秒和小数部分微秒
YYYY-MM-DDThh:mm:ss.sTZD (eg 1997-07-16T19:20:30.45+01:00)

占位符说明：

 YYYY = four-digit year
 MM   = two-digit month (01=January, etc.)
 DD   = two-digit day of month (01 through 31)
 hh   = two digits of hour (00 through 23) (am/pm NOT allowed)
 mm   = two digits of minute (00 through 59)
 ss   = two digits of second (00 through 59)
 s    = one or more digits representing a decimal fraction of a second
 TZD  = time zone designator (Z or +hh:mm or -hh:mm)

此配置文件定义了处理时区偏移的两种方式：
* 时间以协调世界时（UTC）表示，带有特殊的UTC标识符（"Z"）。
* 时间以当地时间表示，加上小时和分钟的时区偏移量。"+hh:mm"的时区偏移表示日期/时间使用的是比协调世界时提前"hh"小时"mm"分钟的当地时区。"-hh:mm"的时区偏移表示日期/时间使用的是比协调世界时晚"hh"小时"mm"分钟的当地时区。

## Examples

1994-11-05T08:15:30-05:00 对应于1994年11月5日上午8点15分30秒，美国东部标准时间。

1994-11-05T13:15:30Z 对应着同一时间。