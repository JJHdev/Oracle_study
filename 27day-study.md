# 1. 테이블 문법
--복사생성
* CREATE TABLE 테이블명
  AS
  SUBQUERY;

-- 테이블 생성, 수정, 삭제
* CREATE TABLE 테이블명(
    컬럼명 데이터타입(크기),
    컬럼명 데이터타입(크기) default 기본값,
    컬럼명 데이터타입(크기) [constraint 제약조건명] 제약조건
    );

--테이블 수정 (컬럼 수정)
* ALTER table 테이블명
--추가 ADD
--변경 MODIFY
--삭제 DROP

--테이블 삭제
* DROP TABLE 테이블명;

--제약조건 조회
SELECT * 
FROM user_constraint
```
##-----------------TABEL 실습------------------------
* 테이블복사
CREATE table emp10
as
select empno,ename,hiredate,deptno
from emp
where deptno = 10
order by hiredate asc;

* 테이블 컬럼추가
alter table emp10
add etc number(10);
 
* 테이블 컬럼변경
  -- 컬럼의 데이터타입, 크기변경시에는 데이터가 있는지 살펴봐야한다.(안에 데이터 해치지 않는 범위에서 변경)
alter table emp10
modify etc varchar2(100);

*테이블 컬럼 삭제
alter table emp10
drop column etc;

-- 테이블복사 생성시 조건이 FALSE이면 데이터 복사 X
create table empsales
as
select empno no, ename name, job
from emp
where 1=2;

CREATE TABLE emp2
AS
select empno eno,ename,job,mgr mgrid,hiredate,sal
from emp
where 1>20;

## 2. 
-------제약조건 (CONSTRAINT)
-- 제약조회
SELECT constraint_name, constraint_type, table_name
FROM   USER_CONSTRAINTS;

1) not null : null허용x
2) unique   : 유일한 값 → 중복허용 x
3) check    : 조건을 만족하는지 체크// check(조건
4) primary key : p.k식별키 (1), (2)을 포함하고 테이블대표, 각 레코드를 구분
-주의 : 컬럼타입 (문자&날짜는 '')
     : 컬럼크기 (숫자 &영문 1글자는 1byte, 한글은 설정에 따라 (2,3byte)
5) FOREIGN KEY : FK.외래키
-PK (혹은 UNIQUE)컬럼의 값을 참조(referance key)

--------수정
-- emp2테이블에 제약조건 추가(변경) -p71
문법 >alter table 테이블명
add [constraint 타입_테이블명_컬럼명(그냥 이름방식)] 제약조건(컬럼명);

-- emp2테이블의 eno에 pk제약조건 추가(변경)
문법 >
alter table emp2
add constraint pk_emp2_eno primary key(eno);

-- emp2테이블의 mgrid에 uq제약조건 추가(변경)
문법 >
alter table emp2
add constraint uq_emp2_eno unique(mgrid);

-- emp2테이블의 sal에 check제약조건 추가(변경)
문법 >
ALTER  table emp2  
ADD constraint ck_emp2_sal check(sal between  700 and 9999);

--emp2테이블의 ename에 nn제약조건 추가  // 다른 제약조건과 다르게 쓰는방법다름
문법 >
ALTER table 테이블명
MODIFY 컬럼명 default 값;

alter table emp2
modify ename constraint nn_emp2_ename not null;

--emp2테이블의 hiredate에 기본값 오늘 추가
alter table emp2
modify hiredate default sysdate;

----------------------인덱스-------------- P75
문법 > 
CREAT[UNIQUE]INDEX [스키마명.]인덱스명
ON [스키마명.]테이블명(컬럼1,컬럼2...)

----------------- 제약조건이 있는 테이블에 DML
입력
INSERT INTO EMP2
values (1,'홍길동',null,null,null,null);

--eno컬럼의 값을 null값 입력
INSERT INTO EMP2
values (4,'김구',null,3,sysdate,700);

수정
update emp2
set sal = 700
where ename ='강감찬';

조회
SELECT 컬럼,
FROM 테이블명

-- emp2테이블에 deptno number(2)컬럼을 추가
-- foreign key
-- fk는 null,중복 허용한다.
-- fk인 컬럼이 참조한다 부모테이블의 pk컬럼을
-- fk인 컬럼명 references 부모테이블의 pk컬럼명
alter table 테이블명
add constraint 제약조건명 foreign key(컬럼명) references 부모(참조)테이블명(컬럼명);

alter table emp2
add deptno number(2);

-- emp2에 deptno fk추가.
alter table emp2
add constraint fk_emp2_deptno foreign key(deptno) references dept(deptno);

-- emp2테이블에 사원입사
insert into emp2(eno, ename,deptno)
values (6,'이도',99); --부서.deptno컬럼에 99값은 없어서 오류 발생.

-- emp2테이블에  삭제
delete from emp2
where eno = 5;

select *
from emp2;

-- 부모에 부서(91입력)
insert into dept(deptno,dname)
values (91,'dept91');

-- 부모에 91번을 99로 변경
update dept
set deptno = 1
where deptno = 90;

-- 부모와 자식관계, pk와 fk인 컬럼에서 입력,수정,삭제
      부서(dept.deptno)          사원(emp2.deptno)
    --------------------------------------
    
입력 : 언제든 가능하다.             존재하는 부모키에 한해 가능(단, null가능)
수정 : 자식이 참조하고 있으면 안된다.  존재하는 부모키에 한해 가능(단, null가능)
삭제 : 자식이 참조하고 있으면 안된다.  언제든 가능하다.


delete from dept
where deptno = 99;

---------------------------------------------------
*테이블생성 : 부모테이블 → 자식테이블 순으로 생성
*부모테이블의 pk컬럼의 데이터입력 → 자식테이블의 fk컬럼의 데이터입력 순

*테이블삭제 : 자식테이블 → 부모테이블 순으로 삭제
*참조하는 child record가 존재해도 부모테이블의 데이터를 삭제하고 싶다면 
  방법1. fk제약조건삭제 후 부모테이블의 데이터삭제
  방법2. 참조하는 child record도 같이 삭제
  
*fk제약조건 설정시 [on delete cascade]
constraint 제약조건명
 foreign key(컬럼명) references 참조테이블명(컬럼명)
[on delete cascade];
  
* 제약조건이 있는 테이블생성 
문법
CREATE TABLE 테이블명(
 컬럼명 데이터타입 (크기) [DEFAULT 기본값] [CONSTRAINT 제약조건명 제약조건],
 컬럼명 데이터타입 (크기) [DEFAULT 기본값] [CONSTRAINT 제약조건명 제약조건]
);
 EX) constraint 제약조건명 primary key(컬럼명)
     constraint 제약조건명 unique(컬럼명)
     constraint 제약조건명 check(컬럼명 조건)
     constraint 제약조건명 foreign key(컬럼명) references 부모테이블명(pk컬럼명)
     constraint 제약조건명 not null(컬럼명)   
     
* 삭제한 테이블 복원 
문법 > 
FLASHBACK TABLE 복원테이블명 TO BEFORE DROP;

FLASHBACK TABLE EMP10 TO BEFORE DROP;

DROP TABLE EMP10;

*휴지통비우기
-- 휴지통을 비우게 되면 복원이 아예 불가능.
문법 > 
PURGE RECYCLEBIN;

## 3.트랜잭션  -p375 중요.
---------------------------------------------------
*TRANSACTION (트랜잭션), 예외처리
EXCEPTION WHEN 예외명1 THEN 예외처리 구문1
          WHEN 예외명2 THEN 예외처리 구문2
