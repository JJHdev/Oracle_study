## 1. 기본 join문 복습
```
-- 1번 기본 조인문법
select d.deptno, d.dname, d.loc, e.empno, e.ename, e.sal
from emp e, dept d
where loc =  upper('new york') and 
      d.deptno = e.deptno
order by e.sal desc;

-- 2번 조인 on 문법
select d.deptno, d.dname, d.loc, e.empno, e.ename, e.sal
from emp e join dept d on loc =  upper('new york') 
                    and 
                    d.deptno = e.deptno
order by e.sal desc;

-- 3번 natural 조인문법
select deptno, d.dname, d.loc, e.empno, e.ename, e.sal
from emp e natural join dept d
where loc =  upper('new york')
order by e.sal desc;

-- 4번 조인문법
select deptno, d.dname, d.loc, e.empno, e.ename, e.sal
from emp e join dept d using(deptno)
where loc =  upper('new york')
order by e.sal desc;

-- 1번 문제
select e.deptno, d.dname, count(e.deptno)
from emp e join dept d on e.deptno=d.deptno
where e.deptno is not null
group by e.deptno, d.dname
having count(e.deptno)>=(select count(deptno)
                        from emp
                        where deptno=(select deptno
                                      from emp
                                      where ename=upper('allen')))
order by deptno;

--- 다중행 서브쿼리 서브쿼리의 결과로 리턴받은 rows수가 여러개일때 (in)(any)(all)
-- in    : 조건에 일치하는것만 출력
-- any   : 조건에 하나라도 만족하면 출력
-- all   : 모든조건이 만족해야 출력
-- exists : 서브쿼리의 실행결과가 있으면 전체출력. (왼쪽 비교대상 컬럼은 작성하지 않는다.)
sal=2450 or sal=5000 or sal=1300 → sal in(2450,5000,1300)
```

## 2. view
```
*view 객체(p73)
view : 하나 이상의 테이블이나 다른 뷰의 데이터를 볼수 있게 하는 데이터베이스 객체이다.

*view생성
create [or replace ]view 뷰명
as
subquery;

*생성
-- 없으면 만들고 있으면 수정해라.
create [or replace ]객체타입 객체명

*수정
alter 객체타입 객체명

*view삭제 문법
drop view 뷰명;

*삭제
drop 객체타입 객체명

-- 시스템으로 시작
grant create view 
to scott;

-- scott로 시작.
create view emp_dept
as
select e.empno, e.ename, d.dname
from emp e, dept d
where e.deptno = d.deptno
      and
      e.sal >= 2450;
      
-- view 삭제
drop view emp_dept;
      
-- view 수정하기.
create or replace view emp_dept
as
select e.empno, e.ename, d.dname, e.sal
from emp e, dept d
where e.deptno = d.deptno
      and
      e.sal >= 2450;

-- view명으로 수정하기. (원본테이블에 영향을 준다.)
-- view에서 수정시 sal 2450이하로 업데이트하면 사라진다. (서브쿼리의 역할을 하며, 만든조건이 2450이상만 만들어서
update emp_dept
set sal=100
where empno = 7698; 

update emp
set sal=3000
where empno = 7698; 

-- 모든 view 조회 ,여기서 read_only가 y이면 update, delete는 못한다.
문법 select view_name, text, read_only
    from user_views;

*p198 인라인뷰(inline view) 
-- view는 테이블에 넣을수있다. (inline view p198 공부)
-- from절에 서브쿼리(subquery)가 들어가면 inline view

-- 부서명, 평균급여, 총급여를 오름차순정렬하여 출력 이때 평균급여는 소수 첫째자리에서 반올림
select deptno, round(avg(sal),0), sum(sal)
from emp
where deptno is not null
group by deptno;

select  d.dname, e.avg_sal, e.sum_sal
from dept d, (select deptno, round(avg(sal),0) avg_sal, sum(sal) sum_sal
              from emp
              where deptno is not null
              group by deptno) e
where d.deptno = e.deptno
order by d.deptno asc;
```

## 3. 계정 접속및 방법
#### hr 계정 lock 푸는법

- system 계정 접속.
문법 conn system/비번

- 계정 정보 비번 및 계정잠금 풀기
문법 alter USER 유저명 identified by 비번 ACCOUNT UNLOCK[LOCK];

alter USER HR identified by hr ACCOUNT UNLOCK;


## 4.  Sequence (시퀀스 p83)
```
-- Sequence (시퀀스 p83)

-- 자동순번을 반환하는 데이터베이스 객체이다.
사용예) 사원번호, 부서번호, 회원번호, 주문번호

*시퀀스조회
SELECT *
FROM USER_SEQUENCES;

*수정
alter SEQUENCE [스키마명.]시퀀스명
start with 초기값   -- (수정불가)
increment by 상승값  -- 
minvalue 최저값      --
maxvalue 최대값;    --
[cycle | nocycle]
[cyche | nocache]

*생성
create [or replace ] SEQUENCE [스키마명.]시퀀스명

*삭제
drop SEQUENCE [스키마명.]시퀀스명;

--부서DEPT테이블의 deptno컬럼의 값으로 사용하고자하는 시퀀스 생성
-- 71부터 81까지 1씩증가.
create SEQUENCE scott.dept_deptno_SEQ
increment by 1
minvalue 1
maxvalue 81;

-- 시퀀스 재 설정 > 재설정 해도 그전에 이어오던 숫자 그대로 간다.
alter SEQUENCE scott.dept_deptno_SEQ
--start with 71   -- (수정불가)
increment by 2  -- 
minvalue 1      --
maxvalue 89    --


------------ 시퀀스 이용한 삽입.
insert into dept(deptno,dname)  
values (dept_deptno_SEQ.nextval, 'dept1');
insert into dept(deptno,dname)  
values (dept_deptno_SEQ.nextval, 'dept2');
insert into dept(deptno,dname)  
values (dept_deptno_SEQ.nextval, 'dept3');

------------ 다른테이블에 시퀀스이용넣기. (위에서 73까지 넣고 아래에선 74 삽입됨)
------------ select에서 그리고 다른 테이블에서 시퀀스 사용해도 숫자 1이 추가된다.
------------ 삭제해도, 롤백해도 구멍이 매꿔지진 않으므로 그리하여 구멍이 생길수 있다.

insert into emp(empno,ename)
values (dept_deptno_SEQ.nextval, 'emp2');  -- 76

select dept_deptno_SEQ.nextval from dual;  -- 77

insert into emp(empno,ename)
values (dept_deptno_SEQ.nextval, 'emp2');  -- 78

--시퀀스 삭제
drop SEQUENCE scott.dept_deptno_SEQ;
```

## 5. 시퀀스 자바연동으로 사용하기.
#### 5-1. 오라클 디벨로퍼 문
```
-- emp의 empno에 사용될 시퀀스 생성(java사용)
create sequence scott.empno_SEQ
start with 9100
increment by 1;

-- java에서 사용될 insert문
insert into emp(empno,ename,job ,hiredate,sal,comm)
values (scott.empno_SEQ.nextval, '장길산','축선',sysdate,5000,1500);
```

#### 5-2. (5-1)을 이용하여 자바에 연동하여 넣기
```
//3. QUERY실행조작을 위한 준비조작(객체준비 -Statement객체, PreparedStatement객체)
		String sql ="insert into emp(empno,ename,job ,hiredate,sal,comm) "+
					"values (scott.empno_SEQ.nextval,?,?,sysdate,?,?)";
		try {
			stmt= conn.prepareStatement(sql);
		} catch (SQLException e1) {
			e1.printStackTrace();
		}
    
    
 //4. QUERY실행
    try {
			stmt.setString(1, "장길");
			stmt.setString(2, "축신");
			stmt.setInt(3, 6000);
			stmt.setInt(4, 2500);
			int resultCnt = stmt.executeUpdate();
			System.out.println("QUERY실행결과로 받은 record수 ="+resultCnt);
		} catch (SQLException e1) {
			System.out.println("executeUpdate()실행관련 에러");
			e1.printStackTrace();
		}
```
