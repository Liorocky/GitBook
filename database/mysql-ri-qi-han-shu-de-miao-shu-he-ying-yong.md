# MySql日期函数的描述和应用

## 应用

> 返回第【?】个月的第一天（从当月算起，第一个月指当月）

```text
DATE_ADD( LAST_DAY( now( ) - INTERVAL ? MONTH ), INTERVAL 1 DAY )
```

> 返回【?】个月前的那一天

```text

DATE_SUB( CURDATE( ), INTERVAL ? MONTH )
```

## 描述

| 函数 | 描述 |
| :--- | :--- |
| [DATE\_ADD\(\)](https://www.docs4dev.com/docs/zh/mysql/5.7/reference/date-and-time-functions.html#function_date-add) | 两个日期相加 |
| [LAST\_DAY\(\)](https://www.docs4dev.com/docs/zh/mysql/5.7/reference/date-and-time-functions.html#function_last-day) | 返回参数的月份的最后一天 |



