1. 전날 복습 (substr, instr)
  함수에서 생략이란 뒤에서부터 가능하다.
  

 ```
 2. instr
 문법(INSTR(str,substr,pos,occur)
   - pos는 생략가능. 생략하면 1적용 > 첫번째 문자부터 찾겠다. occur은 몇 번째 같은지 반환
        select instr('apple','a',1,1) as "1번",
               instr('apple','a',1) as "2번",
               instr('apple','a') as "3번"
        from dual;
 ```
 

 ```
 3. 오라클 9i r2부터 with절 사용.
   임시테이블(가상테이블)로서 사용.
   WITH 테이블명 AS (
    SELECT 쿼리문
    UNION ALL
    SELECT 쿼리문
    )
    SELECT 쿼리문;
    
    
    WITH table1 AS(
    SELECT '02)123-4567' AS t1 FROM dual
    UNION ALL
    SELECT '031)123-4567' AS t1 FROM dual
    ) 
    select t1, substr(t1,1,instr(t1,')',1,1)) 	  from table1;
    
 ```
 
 4. Oracle과 Java DM 연
 
 
