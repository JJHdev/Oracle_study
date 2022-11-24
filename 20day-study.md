select {컬럼명,* ,| , [as}}
from tab, 테이블명
where 조건절
group by [select 컬럼 같은 그룹]
having [그룹조건]
order by 정렬절 정렬기준

 → having 절은 무조건 그룹절이 있어야 사용 가능핟.

1. select distinct deptno → (10,10,10,20,20) → (10,20)

2. where deptno is not null → null이 아닌 컬럼만 나타내라.

3. 반올림, 반내림 
  3-1. ceil(avg(sal)) > 반 올림
  3-2. floor(avg(sal)) > 반 내림
  3-3. round(~,[소수점 기준]) > 반 올림 (소수점 첫번째자리는 0이다.)
  
4. having 조건 작성 법 (between, and)

```
4-1.
select job, max(sal), min(sal)
from emp
group by job
having max(sal) between 1600 and 3000
order by  max(sal) desc

JOB                  MAX(SAL)   MIN(SAL)
------------------ ---------- ----------
ANALYST                  3000       3000
MANAGER                  2975       2450
SALESMAN                 1600       1250

4-2.
select job, max(sal), min(sal)
from emp
group by job
having max(sal) >= 1600 and max(sal)<= 3000
order by  max(sal) desc

JOB                  MAX(SAL)   MIN(SAL)
------------------ ---------- ----------
ANALYST                  3000       3000
MANAGER                  2975       2450
SALESMAN                 1600       1250
```

4-3. where과 having 절 사용중 where을 사용할수 잇으면 먼저 하는게 좋다.! (효율성.)

5. to_number('10')
 → 문자를 숫자로 변경하는 함수.

6. to_char(deptno)
 → 숫자를 문자로 변경하는 함수. 

* 함수
 → 내장함수
 → 사용자정의 함수 
 
 → 암묵적함수
 → 명시적함수
 
 → 문자함수
  - char
  - varchar2
 → 숫자함수
  - number
 → 날짜함수
  - date
 
* 형변환함수
 to_number(문자) : 문자 → 숫자
 to_char(숫자) : 숫자 → 문자, 날짜 → 문자 
 to_date(문자) : 문자 → 날짜

```
7. intersect 문법 (교집합 부분 출력)

SQL> select deptno
  2  from dept
  3  intersect
  4  select deptno
  5  from emp;

    DEPTNO
        10
        20
        30
        
8. 
 minus 문법 (차집합 부분 출력)
 union 문법 (합집합 부분 출력)
 union all 문법 (합집합 중복까지 출력)
```

9. oracle 홈페이지에서 로그인 후 sql (다운 JDK 버전만 맞으면 된다.)

→  https://github.com/gilbutitbook/006696 홈페이지 들어가서 code 다운받고 DB관련 파일에 압축풀기. → 명령프롬프트 들어가기 (cd 명령주소(C:\backup\01장 환경설정)) 와 같이 들어가기.

10. 시스템 붙은 후 오라유저 스페이스 생성하기.

sqlplus system/asdf123
conn system/asdf123
create user ora_user
identified by hong
default tablespace myts
temporary tablespace temp;

- 위의 내용 요약
 - 사용자명 : ora_user
 - 비번 : hong
 -기

 -1) export :백업하기.
  - 전체 백업하기 or 사용자단위로 백업
  - 모든 테이블스페이스, 사용자, 객체, 데이터 포함
  - 문법 >exp userid = 유저명/ 비번 file = '경로/파일명.dmp' [full=y]
