# Oracle_study
#### * DBMS란?
Data Base Management System
- oracle, MY-sql, MS-sql, Maria

#### * DML
조회
select {*|컬럼명}
from 테이블
[where 조건]
[]
[]
[];

desc 테이블명 : 전체 컬럼조회

* 입력
insert into 테이블명(컬럼명,...컬럼명)
values(값...값);

* 수정
update
[where 조건]

* 삭제
delete
[where 조건]

* [ORDER BY 정렬기준 정렬방법];
 정렬방법
  - 오름차순(asc 기본) : 숫자 작 → 큰 (A → Z),(ㄱ → ㅎ),(예전 → 최근)
  - 내림차순(desc) : 숫자 큰 → 작 (Z → A),(ㅎ → ㄱ),(최근 → 예전)
```
  SQL> select deptno,dname,loc
  2  from dept
  3  order by deptno desc
  4  /

    DEPTNO DNAME                        LOC
---------- ---------------------------- ---------------
        40 OPERATIONS                   BOSTON
        30 SALES                        CHICAGO
        20 RESEARCH                     DALLAS
        10 ACCOUNTING                   NEW YORK

SQL> ed
Wrote file afiedt.buf

  1  select deptno,dname,loc
  2  from dept
  3* order by deptno asc
SQL> /

    DEPTNO DNAME                        LOC
---------- ---------------------------- ---------------
        10 ACCOUNTING                   NEW YORK
        20 RESEARCH                     DALLAS
        30 SALES                        CHICAGO
        40 OPERATIONS                   BOSTON

```
*null은 무한대
-값에 null이 포함된 상태에서 숫자 오름차순이면 무한대이므로 가장 마지막에 출력
-null이 포함된 계산결과는 null (무한대이므로)
 
* 데이터 분석방법
 -데이터를 보며 분석
 -서로의 상관관계를 보면서 익히기.
 
 ★★★★★ (join, subquery)
 ★★★★★ (DML)
 ★★★★ 함수 
 
*현재날짜 시간 조회
select sysdate
from dual;

*null과 관련된 함수
 nvl(표현식(컬럼명or값or계산식) ,null 대체 값)

 nvl2(표현식(컬럼명or값or계산식) ,null아닌 대체값,null대체 값)
 
 nullif(표현식1,표현식2) 표현식1이 표현식2와 같다면 null을 return 같지 않다면 표현식1을 return하겠다.
 
 Coalesce(표현식1,표현식2,...) null이 아닌 최초의 표현식을 리턴
```
  1  select sal, comm, nvl(comm,10),nvl2(comm,10,20), nullif(sal,comm)
  2* from emp
SQL> /

       SAL       COMM NVL(COMM,10) NVL2(COMM,10,20) NULLIF(SAL,COMM)
---------- ---------- ------------ ---------------- ----------------
       800                      10               20              800
      1600        300          300               10             1600
      1250        500          500               10             1250
      2975                      10               20             2975
      1250       1400         1400               10             1250
      2850                      10               20             2850
      2450                      10               20             2450
      5000                      10               20             5000
      1500          0            0               10             1500
       950                      10               20              950
      3000                      10               20             3000
####       1300                      10               20             1300
```

*nvl(comm,0)+sal*12+sal*0.03 "year sal"
- 여기서 yearsal은 별칭으로 앞에 함수를 대체해서 표현한다.
- 단어사이를 띄우고 싶으면 ""로 묶어준다.

*alias(별칭)
: 컬럼의 별칭. 복잡한 컬럼을 명료하게 명명. 이해도 높이고, 간결
문법 > 컬럼명 [AS] 별칭
"별칭": 공백이 있을경우, 소문자, 대소문자, 특수문자 표현하고싶을 경우

* where
```
  1  select empno, ename
  2  from emp
  3* where empno!=7369
SQL> /

     EMPNO ENAME
---------- --------------------
      7499 ALLEN
      7521 WARD
      7566 JONES
      7654 MARTIN
      7698 BLAKE
      7782 CLARK
      7839 KING
      7844 TURNER
      7900 JAMES
      7902 FORD
      7934 MILLER
```


*비교연산자
a=b 같다 (문자를 같다고 표현할떄 문자를 ' ' 묶어줘야한다.)
a!=b or a<>b 같지않다
a>b a는 b보다 크다 (초과)
a>=b a는 b보다 크거나 같다(이상)
a<b a는 b보다 작다 (미만)
a<=b a는 b보다 작거나 같다 (이하)
문자를 

*대소문자
upper('  ') 대문자
lower('  ') 소문자
initcap('  ' ) 첫문장만 대문자
concat(1~,2~) : 1~과 2~를 붙여준다.  
```
  1  select empno,ename,lower(ename),upper('ford')
  2  from emp
  3* where ename = upper('ForD')
SQL> /

     EMPNO ENAME                LOWER(ENAME)         UPPER('F
---------- -------------------- -------------------- --------
      7902 FORD                 ford                 FORD
```

*문법
select 컬럼명
from 테이블명
[where 조건]
[order by 컬럼명 오름(내림)차순명]
