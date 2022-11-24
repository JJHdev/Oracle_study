1. oracle 문법
  select 컬렴명 {*,|,[as] alias)
  from 테이블명
  [where 조건]
  [group by 그룹기준]
  []
  [order by 정렬기준 정렬방식]
  
 *복습
 
 - 연결연산자 ( 서로 글을 || ':' || 가깝게 연결 )
 - or	  → 나열된 조건중에 단 1개 이상을 만족시 →true 리턴
 - and    → 나열된 조건중에 모두 만족시 → true 리턴
          → 단 1개의 조건이 false → false 리턴
 - not    → 주어진 조건을 제외 한 모든 값.
   in     → 컬럼명 in(값, 값,...) 값에 해당하면 리턴
   not in →not in(값, 값,...) 값에 해당하지 않으면 리턴
   is null → null만 리턴 (where comm is null)
   is not null → null이 아닌 값 리턴 (where comm is not null)
   

 ```
 - 애매모호한 조건 % → 0글자 이상의 자릿수
   like → 이것 과 같은 값 반영
   아래는 맨앞에 W를 가지고 뒤에 몇글자가 오던지 리턴해달라 요청. (%위치를 바꿔도 된다.)
  1  select *
  2  from emp
  3* where ename like 'W%'
     EMPNO ENAME                JOB                       MGR HIREDATE        SAL       COMM     DEPTNO
      7521 WARD                 SALESMAN                 7698 81/02/22       1250        500         30
      
 - 2번째 글자에 A가 나오게 하는 방법 ( '_' )
   '_A%'
```
   
  
 
        
``` 
 - 부서번호가 40이거나 30인 조건 
  1  select deptno, dname
  2  from dept
  3* where deptno = 40 or deptno = 30
SQL> /

    DEPTNO DNAME
        30 SALES
        40 OPERATIONS
```

```
- Between a and b : a와 b사이
  1  select *
  2  from emp
  3  where deptno =30
  4           and
  5*          sal BETween 1250 and 1600
SQL> /

     EMPNO ENAME                JOB                       MGR HIREDATE        SAL       COMM     DEPTNO
      7499 ALLEN                SALESMAN                 7698 81/02/20       1600        300         30
      7521 WARD                 SALESMAN                 7698 81/02/22       1250        500         30
      7654 MARTIN               SALESMAN                 7698 81/09/28       1250       1400         30
      7844 TURNER               SALESMAN                 7698 81/09/08       1500          0         30
```


2. set pagesize 100; (페이지 사이즈 조절)


 ```
3. 오름차순 여러번 적용법
 컬럼명 정렬기준, 컬럼명 정렬기준
  1  select *
  2  from emp
  3  where HIREDATE >= '81,06,25' or empno=7369 or sal >3000
  4* order by sal asc, deptno asc
SQL> /

     EMPNO ENAME                JOB                       MGR HIREDATE        SAL       COMM     DEPTNO
---------- -------------------- ------------------ ---------- -------- ---------- ---------- ----------
      7369 SMITH                CLERK                    7902 80/12/17        800                    20
      7900 JAMES                CLERK                    7698 81/12/03        950                    30
      7654 MARTIN               SALESMAN                 7698 81/09/28       1250       1400         30
      7934 MILLER               CLERK                    7782 82/01/23       1300                    10
      7844 TURNER               SALESMAN                 7698 81/09/08       1500          0         30
      7902 FORD                 ANALYST                  7566 81/12/03       3000                    20
      7839 KING                 PRESIDENT                     81/11/17       5000                    10

7 rows selected.
```

```
4. insert
 - insert into 테이블명[(컬럼명,...컬럼명)]
   value(값,...값);
  1  insert into dept(deptno,dname,loc)
  2* values(90,'dept90','seoul')
SQL> /
1 row created.
```

 ```
4-1. escape
   '~~~~ $%~~ $_~~~' escape '$'
와일드카드의  _ 와 % 가 아닌 단어를 쓰며 그 의미는 escape 앞에 있는 기호와 뒤에 같은 기호 앞에 있는 특수문자는 컴퓨터에서 그대로 받아들이게 해야한다. (꼭 $를 써야하는건 아니다.)
 
  1  select *
  2  from dept
  3* where dname Like '%$_%' escape '$'
  
    DEPTNO DNAME                        LOC
---------- ---------------------------- --------------------------
        92 dept_92                      DokDo
```

```
5. group by (컬럼명,.....컬럼명)
 - select 컬럼명 count(컬럼명)
 - from emp
 - group by (컬럼명,.....컬럼명) (select와 같음.)
  → group by 절에서 사용된 컬럼이 아닌 일반 컬럼은 select절에서 사용 하면 안된다.
  → 업무별 사원수 조회 방법.
 
  1  select job ,count(job)
  2  from emp
  3* group by job;

      JOB                COUNT(JOB)
      CLERK                       3
      SALESMAN                    4
      PRESIDENT                   1
      MANAGER                     3
      ANALYST                     1
```


```
6. 최고급여, 최저급여, 총급여, 평균급여 조회방법.
 - max, min, sum ,avg, 
  ex) 1
  select job ,count(job), max(sal), min(sal), sum(sal), avg(sal)
  from emp
  group by job
      JOB                COUNT(JOB)   MAX(SAL)   MIN(SAL)   SUM(SAL)   AVG(SAL)
      CLERK                       3       1300        800       3050 1016.66667
      SALESMAN                    4       1600       1250       5600       1400
      PRESIDENT                   1       5000       5000       5000       5000
      MANAGER                     3       2975       2450       8275 2758.33333
      ANALYST                     1       3000       3000       3000       3000
      
  ex) 2    
  select deptno, count(sal), max(sal), min(sal), avg(sal), sum(sal)
  from emp
  group by deptno

    DEPTNO COUNT(SAL)   MAX(SAL)   MIN(SAL)   AVG(SAL)   SUM(SAL)
        30          6       2850        950 1566.66667       9400
        20          3       3000        800 2258.33333       6775
        10          3       5000       1300 2916.66667       8750
        
        
  - count(컬럼명) > null제외 갯수 반영
  - count(*) > null 포함 갯수 반영.
```



```
*cube, rollup
-cube   :  모든 그룹각각 묶어 개별 합계 반영
-rollup :  1차 그룹을 묶어 개별 합계반영.

select deptno, job, count(deptno), count(job)
from emp
group by cube(job,deptno)
order by deptno asc, job asc

    DEPTNO JOB                COUNT(DEPTNO) COUNT(JOB)
        10 CLERK                          1          1
        10 MANAGER                        1          1
        10 PRESIDENT                      1          1
        10                                3          3
        20 ANALYST                        1          1
        20 CLERK                          1          1
        20 MANAGER                        1          1
        20                                3          3
        30 CLERK                          1          1
        30 MANAGER                        1          1
        30 SALESMAN                       4          4
        30                                6          6
           ANALYST                        1          1
           CLERK                          3          3
           MANAGER                        3          3
           PRESIDENT                      1          1
           SALESMAN                       4          4
                                         12         12
---------- ------------------ ------------- ----------

  1  select deptno, job, count(deptno), count(job)
  2  from emp
  3  group by rollup(job,deptno)
  4* order by deptno asc, job asc


    DEPTNO JOB                COUNT(DEPTNO) COUNT(JOB)
        10 CLERK                          1          1
        10 MANAGER                        1          1
        10 PRESIDENT                      1          1
        20 ANALYST                        1          1
        20 CLERK                          1          1
        20 MANAGER                        1          1
        30 CLERK                          1          1
        30 MANAGER                        1          1
        30 SALESMAN                       4          4
           ANALYST                        1          1
           CLERK                          3          3
           MANAGER                        3          3
           PRESIDENT                      1          1
           SALESMAN                       4          4
                                         12         12
```


