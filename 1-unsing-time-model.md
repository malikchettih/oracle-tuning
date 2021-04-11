# Using Time Model

### Top 7 “time model statistics” operations at the system level

```SQL

SQL> COL STAT_NAME FORMAT A43
SQL> SELECT 
       STAT_NAME, 
       TO_CHAR(VALUE,'999,999,999,999') TIME_MICRO_S
     FROM V$SYS_TIME_MODEL
     WHERE 
       VALUE <>0 
       AND STAT_NAME NOT IN ('background elapsed time', 'background cpu time') 
     ORDER BY VALUE DESC
     FETCH FIRST 7 ROWS ONLY;
 
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

### Display the percentage of each operation type from the total DB time

```SQL
 
SQL> COL STAT_NAME FORMAT A43
SQL> SELECT 
       STAT_NAME, 
       TO_CHAR(VALUE,'999,999,999,999') TIME_MICRO_S, 
       ROUND(VALUE/(SELECT VALUE FROM V$SYS_TIME_MODEL WHERE STAT_NAME='DB time')*100,2) PCT
     FROM V$SYS_TIME_MODEL
     WHERE 
       VALUE <>0 AND 
       STAT_NAME NOT IN ('background elapsed time', 'background cpu time')
     ORDER BY VALUE DESC
     FETCH FIRST 7 ROWS ONLY;
 
STAT_NAME                                   TIME_MICRO_S            PCT
------------------------------------------- ---------------- ----------
DB time                                           12,934,313        100
sql execute elapsed time                           8,242,126      63,72
DB CPU                                             6,646,626      51,39
parse time elapsed                                 1,928,192      14,91
hard parse elapsed time                            1,830,532      14,15
connection management call elapsed time              305,607       2,36
PL/SQL compilation elapsed time                      154,742        1,2
 
7 rows selected

```

### Display the time model statistics in a hierarchy structure

```SQL

SQL> col STAT_NAME format a60
SQL> SELECT LPAD(' ', 2*level-1)||STAT_NAME STAT_NAME, 
         TRUNC(VALUE/1000000,2) SECONDS 
    FROM (
  SELECT 0 id, 9 pid, null STAT_NAME, null value FROM dual union
  SELECT DECODE(STAT_NAME,'DB time',10) ID,
         DECODE(STAT_NAME,'DB time',0) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'DB time' union
  SELECT DECODE(STAT_NAME,'DB CPU',20) ID,
         DECODE(STAT_NAME,'DB CPU',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'DB CPU' union
  SELECT DECODE(STAT_NAME,'connection management call elapsed time',21) ID,
         DECODE(STAT_NAME,'connection management call elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'connection management call elapsed time' union
  SELECT DECODE(STAT_NAME,'sequence load elapsed time',22) ID,
         DECODE(STAT_NAME,'sequence load elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'sequence load elapsed time' union
  SELECT DECODE(STAT_NAME,'sql execute elapsed time',23) ID,
         DECODE(STAT_NAME,'sql execute elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'sql execute elapsed time' union
  SELECT DECODE(STAT_NAME,'parse time elapsed',24) ID,
         DECODE(STAT_NAME,'parse time elapsed',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'parse time elapsed' union
  SELECT DECODE(STAT_NAME,'hard parse elapsed time',30) ID,
         DECODE(STAT_NAME,'hard parse elapsed time',24) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'hard parse elapsed time' union
  SELECT DECODE(STAT_NAME,'hard parse (sharing criteria) elapsed time',40) ID,
         DECODE(STAT_NAME,'hard parse (sharing criteria) elapsed time',30) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'hard parse (sharing criteria) elapsed time' union
  SELECT DECODE(STAT_NAME,'hard parse (bind mismatch) elapsed time',50) ID,
         DECODE(STAT_NAME,'hard parse (bind mismatch) elapsed time',40) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'hard parse (bind mismatch) elapsed time' union
  SELECT DECODE(STAT_NAME,'failed parse elapsed time',31) ID,
         DECODE(STAT_NAME,'failed parse elapsed time',24) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'failed parse elapsed time' union
  SELECT DECODE(STAT_NAME,'failed parse (out of shared memory) elapsed time',41) ID,
         DECODE(STAT_NAME,'failed parse (out of shared memory) elapsed time',31) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'failed parse (out of shared memory) elapsed time' union
  SELECT DECODE(STAT_NAME,'PL/SQL execution elapsed time',25) ID,
         DECODE(STAT_NAME,'PL/SQL execution elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'PL/SQL execution elapsed time' union
  SELECT DECODE(STAT_NAME,'inbound PL/SQL rpc elapsed time',26) ID,
         DECODE(STAT_NAME,'inbound PL/SQL rpc elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'inbound PL/SQL rpc elapsed time' union
  SELECT DECODE(STAT_NAME,'PL/SQL compilation elapsed time',27) ID,
         DECODE(STAT_NAME,'PL/SQL compilation elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'PL/SQL compilation elapsed time' union
  SELECT DECODE(STAT_NAME,'Java execution elapsed time',28) ID,
         DECODE(STAT_NAME,'Java execution elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'Java execution elapsed time' union
  SELECT DECODE(STAT_NAME,'repeated bind elapsed time',29) ID,
         DECODE(STAT_NAME,'repeated bind elapsed time',10) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'repeated bind elapsed time' union
  SELECT DECODE(STAT_NAME,'background elapsed time',60) ID,
         DECODE(STAT_NAME,'background elapsed time',0) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'background elapsed time' union
  SELECT DECODE(STAT_NAME,'background cpu time',61) ID,
         DECODE(STAT_NAME,'background cpu time',60) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'background cpu time' union
  SELECT DECODE(STAT_NAME,'RMAN cpu time (backup/restore)',62) ID,
         DECODE(STAT_NAME,'RMAN cpu time (backup/restore)',61) PID , STAT_NAME, VALUE
    FROM v$sys_time_model
   WHERE STAT_NAME = 'RMAN cpu time (backup/restore)')
  CONNECT BY PRIOR id = pid START WITH id = 0;
 
STAT_NAME                                                       SECONDS
------------------------------------------------------------ ----------
   DB time                                                        13,02
     DB CPU                                                        6,77
     connection management call elapsed time                       0,31
     sequence load elapsed time                                       0
     sql execute elapsed time                                      8,25
     parse time elapsed                                            1,98
       hard parse elapsed time                                     1,88
         hard parse (sharing criteria) elapsed time                0,02
           hard parse (bind mismatch) elapsed time                    0
       failed parse elapsed time                                   0,02
         failed parse (out of shared memory) elapsed time             0
     PL/SQL execution elapsed time                                 0,01
     inbound PL/SQL rpc elapsed time                                  0
     PL/SQL compilation elapsed time                               0,15
     Java execution elapsed time                                      0
     repeated bind elapsed time                                       0
   background elapsed time                                        60,04
     background cpu time                                          28,49
       RMAN cpu time (backup/restore)                                 0
 
20 rows selected
 
```
