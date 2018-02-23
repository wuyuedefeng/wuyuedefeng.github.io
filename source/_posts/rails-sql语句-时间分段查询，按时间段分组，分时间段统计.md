title: rails sql语句 时间分段查询，按时间段分组，分时间段统计
author: wsen
date: 2018-02-23 17:56:36
tags:
---
```
#按小时分组
select DATE_FORMAT(created_at,'%H') hours,count(id) count from doctors group by DATE_FORMAT(created_at,'%H');
```

时间段字符如下
```
%a - The abbreviated weekday name (''Sun'')
%A - The  full  weekday  name (''Sunday'')
%b - The abbreviated month name (''Jan'')
%B - The  full  month  name (''January'')
%c - The preferred local date and time representation
%d - Day of the month (01..31)
%H - Hour of the day, 24-hour clock (00..23)
%I - Hour of the day, 12-hour clock (01..12)
%j - Day of the year (001..366)
%m - Month of the year (01..12)
%M - Minute of the hour (00..59)
%p - Meridian indicator (''AM'' or ''PM'')
%S - Second of the minute (00..60)
%U - Week  number  of the current year,
        starting with the first Sunday as the first
        day of the first week (00..53)
%W -Week  number  of the current year,
        starting with the first Monday as the first
        day of the first week (00..53) 该句有错误， 滚动下方参考w3c 一年的第几周，以星期一作为一周的开始计算应该是：%u
%w - Day of the week (Sunday is 0, 0..6)
%x - Preferred representation for the date alone, no time
%X - Preferred representation for the time alone, no date
%y - Year without a century (00..99)
%Y - Year with century
%Z - Time zone name
%% - Literal ''%'' character
```

使用实例

```sql
doctor_chart_new = AgentOrder.find_by_sql("
select DATE_FORMAT(table_a.order_time,'%d') df, count(table_a.id) count from
(SELECT * FROM agent_orders
GROUP BY agent_orders.doctor_id
ORDER BY agent_orders.doctor_id asc) as table_a
WHERE (order_time BETWEEN '#{month_beginning}' AND '#{month_end}')
GROUP BY DATE_FORMAT(table_a.order_time,'%d')")
```

```
# w3c 更准确
可以使用的格式有：
格式	描述
%a	缩写星期名
%b	缩写月名
%c	月，数值
%D	带有英文前缀的月中的天
%d	月的天，数值(00-31)
%e	月的天，数值(0-31)
%f	微秒
%H	小时 (00-23)
%h	小时 (01-12)
%I	小时 (01-12)
%i	分钟，数值(00-59)
%j	年的天 (001-366)
%k	小时 (0-23)
%l	小时 (1-12)
%M	月名
%m	月，数值(00-12)
%p	AM 或 PM
%r	时间，12-小时（hh:mm:ss AM 或 PM）
%S	秒(00-59)
%s	秒(00-59)
%T	时间, 24-小时 (hh:mm:ss)
%U	周 (00-53) 星期日是一周的第一天
%u	周 (00-53) 星期一是一周的第一天
%V	周 (01-53) 星期日是一周的第一天，与 %X 使用
%v	周 (01-53) 星期一是一周的第一天，与 %x 使用
%W	星期名
%w	周的天 （0=星期日, 6=星期六）
%X	年，其中的星期日是周的第一天，4 位，与 %V 使用
%x	年，其中的星期一是周的第一天，4 位，与 %v 使用
%Y	年，4 位
%y	年，2 位
```