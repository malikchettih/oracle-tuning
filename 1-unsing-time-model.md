# Using Time Model

### Top 7 “time model statistics” operations at the system level

```SQL

SQL> COL STAT_NAME FORMAT A43
SQL> SELECT STAT_NAME, TO_CHAR(VALUE,'999,999,999,999') TIME_MICRO_S
  2  FROM V$SYS_TIME_MODEL
  3  WHERE VALUE <>0 AND STAT_NAME NOT IN ('background elapsed time', 'background cpu time')
  4  ORDER BY VALUE DESC
  5  FETCH FIRST 7 ROWS ONLY;
 
STAT_NAME                                   TIME_MICRO_S
------------------------------------------- ----------------
DB time                                           12,772,612
sql execute elapsed time                           8,138,883
DB CPU                                             6,432,830
parse time elapsed                                 1,829,384
hard parse elapsed time                            1,738,486
connection management call elapsed time              299,393
PL/SQL compilation elapsed time                      142,613
 
7 rows selected

```
