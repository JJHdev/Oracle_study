## 1.날짜및 숫관련 코드
```
-- 왼쪽 데이터 기준으로, 6개월 전 후 월
select add_months(sysdate,-6), add_months(sysdate,6)
from dual;         

-- 각 월사이의 차이수
select months_between(sysdate,add_months(sysdate,-6))
from dual;

-- 기준 데이터의 마지막 일수계산
select last_day(sysdate)
from dual;

select day_between(last_day(sysdate), add_months(sysdate,-6))
from dual;

select last_day(hiredate)-hiredate
from emp;

---- 비교 : 숫자데이터대상의 trund(), round()
-- 소수점 이하로 절삭.
select trunc(123.456), trunc(456.78)    
from dual;

-- 반올림 값
select round(123.456,-2) , round(123.456,-1),
       -- (100)            --(120)
       round(123.456,0)  , round(123.456,1)
       -- (123)            --(123.5)
from dual;

-- trunc(sysdate, 'month') 월초를 가르킨다. (last_day(sysdate)와 반대의 개념)
-- trunc(sysdate, 'day') 일에서 오전 오후를 기준으로 나눈다.
-- to_char(sysdate, 'YYYY.MM.DD HH24: MI:SS')
select sysdate, -- (22/11/28)
       trunc(systimestamp, 'month'), --(22/11/01)
       trunc(systimestamp, 'day'), --(22/11/27)
       to_char(trunc(systimestamp, 'month'), 'YYYY.MM.DD HH24: MI:SS'), --(2022.11.01 00: 00:00)
       to_char(trunc(systimestamp, 'day'), 'YYYY.MM.DD HH24: MI:SS'), --(2022.11.27 00: 00:00)
       to_char(round(systimestamp, 'month'), 'YYYY.MM.DD HH24: MI:SS'), --(2022.12.01 00: 00:00)
       to_char(round(systimestamp, 'day'), 'YYYY.MM.DD HH24: MI:SS') --(2022.11.27 00: 00:00)
from dual;
```

----------------------------------------------------------------------------------

## 2. join.
```
p176
★★★★★JOIN 

SELECT 컬럼명 [AS] 별칭, 컬럼명 [AS] 별칭
FROM 테이블명 별칭, 테이블명 별칭
[WHERE 조건];

--where 사원테이블의 부서번호와 부서테이블의 부서번호 컬럼의 값이 같다는 조건 써야한다.
--where절에 = 기호를 사용하여 join조건 명시. (동등조인)
-- 조인조건에서 서로 일치하는(=)조건만 조회되고 즉 여기에서는 사원테이블에 부서번호가 null인 사원은 조회되지 않는다.
select empno,ename,job,emp.deptno,dname
from emp, dept
where emp.deptno = dept.deptno;

-- 에러가:column ambiguously defined 발생하면. 소속을 정해주면 없어진다.
-- 조인기법 3가지.
-- 기본 JOIN 문법
SELECT emp.empno,emp.ename,emp.job,dept.deptno,dept.dname
FROM emp, dept
WHERE emp.deptno = dept.deptno
ORDER BY deptno asc;

-- JOIN ON
-- 테이블명을 명시해야 속도가 더 빨라진다.
SELECT e.empno, e.ename, e.deptno, d.dname
FROM emp e JOIN dept d ON e.deptno=d.deptno
ORDER BY deptno ;

-- NUTURAL JOIN 문법
-- 두 테이블에 속하는 컬럼명은 소속을 지정해주지 않는다.
SELECT e.empno, e.ename, deptno, d.dname
FROM emp e NUTURAL JOIN dept d
ORDER BY deptno ;

-- JOIN USING 문법
-- 두 테이블에 속하는 컬럼명은 소속을 지정해주지 않는다.
SELECT e.empno, e.ename, deptno, d.dname
FROM emp e JOIN dept d USING(deptno)
ORDER BY deptno ;

-- JOIN ON
-- 테이블명을 명시해야 속도가 더 빨라진다.
-- JOIN ON
-- 테이블명을 명시해야 속도가 더 빨라진다.
SELECT e.empno, e.ename, e.deptno, d.dname
FROM emp e JOIN dept d ON e.deptno=d.deptno
ORDER BY deptno ;


-- 별칭사용방법(테이블에 별칭을 사용함으로 쿼리를 간단하게 작성가능./ 이 쿼리안에서만 사용가능하다.)
select e.empno,e.ename,e.job,dp.deptno,dp.dname
from emp e, dept dp
where e.deptno = dp.deptno
order by deptno asc;

select e.empno, e.ename, e.sal, d.deptno, d.dname
from emp e, dept d
where e.deptno = d.deptno and (e.deptno = 10  or e.deptno = 20)
order by dname asc;

insert into emp
values (9000,'이순신','dba',null,sysdate,7000,null,null);
commit;

select e.empno, e.ename, e.job, d.deptno,d.dname
from emp e , dept d
where e.deptno(+) = d.deptno;

--- where e.deptno(+) = d.deptno; 내용과 동일
select e.empno, e.ename, e.job, d.deptno,d.dname
from emp e right outer join dept d
on e.deptno = d.deptno;

select e.empno, e.ename, e.job, d.deptno,d.dname
from emp e full outer join dept d
on e.deptno = d.deptno;

-------- 어려운부분 self join (연결된 부분 찾아서 연결)
-- 동일한 테이블을 사용해 조인하는 방법.
select e.empno, e.ename, e.mgr, ta.empno,ta.ename
from emp e, emp ta
where  e.mgr = ta.empno;

```

## 3. 서브쿼리문

```
subquery (서브쿼리 p192)
: 서브쿼리란 SQL문장안에서 보조로 사용되는 또 다른 SELECT문을 의미.
/*
SELECT 컬럼명
FROM 테이블명
[WHERE 조건]
[GROUP BY 그룹기준]
[HAVING 그룹조건]
[ORDER BY 정렬기준 방법] 
*/
select deptno, empno, ename
from  emp
where deptno= (select deptno
               from emp
               where ename = upper('smith'));
               
select empno, ename, job
from emp
where job = (select job
             from emp
             where ename = upper('turner'));

select empno,ename,sal
from emp
where sal > (select sal
            from emp
            where ename = upper('turner'));


select empno,ename,sal
from emp
where sal >(select avg(sal)
            from emp);
            
-- 터너와 같은 직업의 평균급여 이상의 같은직업 출력.      
select empno,ename,sal
from emp
where job =(select job
            from emp
            where ename = upper('turner')) and  sal >  (select avg(sal)
                                                           from emp
                                                           where job = (select job
                                                                        from emp
                                                                        where ename = upper('turner')));
       
-- 터너와 같은 직군의 평균 급여보다 이상으로 받는 같은부서 사람들 출력.                      
select e.empno,e.ename,e.job,e.sal,d.dname
from emp e,dept d
where   e.deptno =d.deptno
        and
        e.sal>=(select avg(sal)
            from emp
            where job =(select job
                        from emp
                        where ename='TURNER'));
```
