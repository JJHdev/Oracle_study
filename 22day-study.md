1. CREATE TABLE 테이블명(
    컬럼명 데이터타입,
    컬럼명 데이터타입 [CONSTRAINT 제약조건명][제약조건],
    컬럼명 데이터타입 [DEFAULT 값] [CONSTRAINT 제약조건명][제약조건]
)[TABLESPACE 테이블스페이스명];

2. insert into 테이블명(넣을 컬럼명,...)
   values (값1,....)
   
3. create table 테이블명(
   컬럼명 값, 컬럼명 값 );
   
4. case when 조건1 then 값1
 		  when 조건2 then 값2
          else 기타값
          end;
          
5. any = or
   some = or
   all = and 
   
```
select ename, sal
from emp
where sal = 2000 or sal = 3000 or sal = 5000;

select ename, sal
from emp
where sal in(2000,3000,5000);

select ename,sal
from emp
where sal >= any(2000, 3000, 5000);

select ename,sal
from emp
where sal >= some(2000, 3000, 5000);

select ename,sal
from emp
where sal >= all(2000, 3000, 5000);
```

6. 다중테이블 insert
 - 문법
  INSERT ALL
 WHEN 조건1 THEN
 	 INTO 복사받을 테이블(넣을 컬럼명 적은갯수일경우) VALUES (복사해주는 컬럼명,..복사해주는 컬럼명)
 WHEN 조건2 THEN
  	INTO 복사받을 테이블(넣을 컬럼명 적은갯수일경우) VALUES (복사해주는 컬럼명,..복사해주는 컬럼명)
 ELSE
 	INTO 복사받을 테이블(넣을 컬럼명 적은갯수일경우) VALUES (복사해주는 컬럼명,..복사해주는 컬럼명)
    (서브쿼리)
  SELECT 복사해주는 총 컬럼명 (*가능)
  FROM 복사해주는 테이블명;

```
① 조건이 없는 다중테이블 (unconditional insert table)
create table emp1(
   eno   number(6),  -- 사원번호
   ename  varchar2(30), --사원명
   pay number(10) -- 급여
);

create table emp2(
    eno  number(6),  -- 사원번호
    task varchar2(30), --업무
    dno number(6) -- 부서번호
);

INSERT ALL
INTO emp1 VALUES (empno,ename,sal)
INTO emp2 VALUES (empno,job,deptno)
select empno,ename,sal,job,deptno
from emp;

② 조건이 있는 다중테이블 (conditional insert table)
insert all
    when deptno=10 then
        into emp10 VALUES(empno, ename, sal, sal*13, deptno)
    when deptno=20 then
        into emp20 values(empno, ename, sal, sal*13, deptno)
    else
        into empetc values(empno, ename, sal, sal*13, deptno)
select *
from emp
where deptno = any (10,20,30);

 - where를 작성하지 않으면 else에서 null포함 그외 모든 값이 다 들어가진다.
 - all     : 조건에 따라 충족하는건 모두다 넣기
 - first   : 맨위에 조건에 충족한 값 넣고 그 값 제외 나머지값 차례대로 넣기.
```

7. SUBSTR(char,pos,len):★★★★★
char의 pos번째 문자부터 len길이만큼 잘라낸 결과를 반환.

8. oracle 복습만 진행.
