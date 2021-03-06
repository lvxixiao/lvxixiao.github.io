# 常用函数



## 字符串函数

| 函数 | 功能 |
| :--- | :--- |
| CONCAT\(S1,S2,...Sn\) | 连接S1,S2,...Sn为一个字符串 |
| INSERT\(str,x,y,instr\) | 将字符串str从第x位置开始,y个字符长的字串替换为字符串instr |
| LOWER\(str\) | 将字符串str中所有字符变为小写 |
| UPPER\(str\) | 将字符串str中所有字符变为大写 |
| LEFT\(str,x\) | 返回字符串str最左边的x个字符 |
| RIGHT\(str,x\) | 返回字符串str最右边的x个字符 |
| LPAD\(str,n,pad\) | 用字符串pad对str最左边进行填充,直到长度为n个字符长度 |
| RPAD | 用字符串pad对str最右边进行填充,直到长度为n个字符长度 |
| LTRIM\(str\) | 去掉字符串str左侧的空格 |
| RTRIM\(str\) | 去掉字符串str行尾的空格 |
| REPEAT\(str,x\) | 返回str重复x次的结果 |
| REPLACE\(str,a,b\) | 用字符串b替换字符串str中所有出现的字符串a |
| STRCMP\(s1,s2\) | 比较字符串s1和s2 |
| SUBSTRING\(str,x,y\) | 返回从字符串str x位置起y个字符长度的字串 |

## 数值函数

| 函数 | 功能 |
| :--- | :--- |
| ABS\(x\) | 返回x的绝对值 |
| CEIL\(x\) | 返回大于x的最小整数值 |
| FLOOR\(x\) | 返回小于x的最大整数值 |
| MOD\(x,y\) | 返回x/y的模 |
| RAND\(\) | 返回0-1内的随机值 |
| ROUND\(x,y\) | 返回参数x的四舍五入的有y位小数的值 |
| TRUNCATE\(x,y\) | 返回数字x截断为y位小数的结果 |

## 日期和时间函数

| 函数 | 功能 |
| :--- | :--- |
| CURDATE\(\) | 返回当前日期 |
| CURTIME\(\) | 返回当前时间 |
| NOW\(\) | 返回当前的日期和时间 |
| UNIX\_TIMESTAMP\(date\) | 返回日期date的UNIX时间戳 |
| FROM\_UNIXTIME | 返回UNIX时间戳的日期值 |
| WEEK\(date\) | 返回日期date为一年中的第几周 |
| YEAR\(date\) | 返回日期date的年份 |
| HOUR\(time\) | 返回time的小时值 |
| MINUTE\(time\) | 返回time的分钟值 |
| MONTHNAME\(date\) | 返回date的月份名 |
| DATE\_FORMAT\(date,fmt\) | 返回按字符串fmt格式化日期date值 |
| DATE\_ADD\(date,INTERVAL expr type\) | 返回一个日期或时间值加上一个时间间隔的时间值 |
| DATEDIFF\(expr,expr2\) | 返回起始时间expr和结束时间expr2之间的天数 |

## 流程函数

| 函数 | 功能 |
| :--- | :--- |
| IF\(value,t f\) | 如果value是真,返回t; 否则返回f |
| IFNULL\(value1,value2\) | 如果value1不为空,返回value1,否则返回value2 |
| CASE WHEN \[value1\] THEN\[result1\]...ELSE\[default\]END | 如果value1是真，返回result1,否则返回default |
| CASE\[expr\]WHEN\[value1\]THEN\[result1\]...ELSE\[default\]END | 如果expr等于value1,返回result1,否则返回default |

## 其他常用函数

| 函数 | 功能 |
| :--- | :--- |
| DATEBASE\(\) | 返回当前数据库名 |
| VERSION\(\) | 返回当前数据库版本 |
| USER\(\) | 返回当前登录用户名 |
| INET\_ATON\(IP\) | 返回IP地址的数字表示 |
| INET\_NTOA\(num\) | 返回数字代表的IP地址 |
| PASSWORD\(str\) | 返回字符串str的加密版本 |
| MD5\(\) | 返回字符串str的MD5值 |

