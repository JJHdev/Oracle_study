## * PL/SQL(P260)
문법
SET SERVEROUTPUT ON;
 --데이터를 가진 2개의 변수의 크기를 비교하여 출력
DECLARE
 --변수선언
 변수명 데이터타입[:=초기값]; --초기값 생략시 NULL
[IS(AS)]
 --선언부 : 변수,
BEGIN
 --실행부
END;

--IF조건문
 if 조건1 then
  조건1만족시 실행코드
 elsif 조건2 then
  조건2만족시 실행코드
 else
  조건만족이 없을때 실행코드
 end if;

--case문 (p277)
--CASE 문(p277)
유형1
    CASE 표현식 
        WHEN 결과1 THEN
           처리문1;
        WHEN 결과2 THEN
          처리문2;
        …
        ELSE
         기타 처리문;
     END CASE;

유형2
    CASE WHEN 표현식1 THEN
              처리문1;
            WHEN 표현식2 THEN
              처리문2;
            …
            ELSE
              기타 처리문;
     END CASE;
* 첫 번째보다는 두 번째 유형을 많이 사용
 
 -- LOOP문(p278)
* 루프를 돌며 로직을 처리하는 반복문
* 구문
   LOOP
      처리문;
      EXIT [WHEN 조건];
   END LOOP;

* 루프를 돌다가 EXIT WHEN 조건을 만나면 루프 종료

-- WHILE문
*  반복문의 일종
* 구문
   WHILE 조건
   LOOP
      처리문;
   END LOOP;
* WHILE 다음 조건이 TRUE인 경우 루프를 돌고, FALSE인 경우 루프를 빠져나간다

-- for문
*  반복문의 일종
* 구문
   FOR 인덱스 IN [REVERSE(최종값에서 > 초기값] 초기값..최종값
   LOOP
      처리문;
   END LOOP;
* IN 다음 조건이 만족할 경우에 루프를 돈다.

-- continue문

*  반복문 내에서 특정 조건에 부합할 때 처리로직을 건너뛰고 상단의 루프 조건으로 건너가 
     루프를 계속 수행할 때 사용
* 사용 예
DECLARE
   vn_base_num NUMBER := 3;
BEGIN
   FOR i IN 1..9 
   LOOP
      CONTINUE WHEN i=5;
      DBMS_OUTPUT.PUT_LINE (vn_base_num || '*' || i || '= ' || vn_base_num * i);
   END LOOP;    
END;

-- 연습
SET SERVEROUTPUT ON;
DECLARE
NUM1 number:=200;
num2 NUMBER:=200;
BEGIN
 dbms_output.put_line('num1변수값은: ' || num1 || '  num1변수값은: ' || num2);
 
 IF num1 > num2 then
        dbms_output.put_line('num1변수값은: 크다 ');
    elsif num1 = num2 then
        dbms_output.put_line('num1변수값은: 같다 ');   
    else
        dbms_output.put_line('num1변수값은: 작다. ');
 END IF;
 
     CASE WHEN num1 > num2 THEN
              dbms_output.put_line('num1변수값은: 크다 ');
            WHEN num1 = num2 THEN
              dbms_output.put_line('num1변수값은: 같다 ');  
            ELSE
              dbms_output.put_line('num1변수값은: 작다. ');
     END CASE;
END;
/

declare
 num1 number := 1;
 dan number := 3;
begin
    loop
        DBMS_OUTPUT.PUT_LINE(dan||'*'||num1||'='||dan*num1);
        num1 := num1+1;
        exit when num1>9;
     end loop;
        dbms_output.put_line('loop 밖');
end;
/

declare
 num1 number := 1;
 dan number := 3;
begin
    while num1<9
    loop
        DBMS_OUTPUT.PUT_LINE(dan||'*'||num1||'='||dan*num1);
        num1 := num1+1;
     end loop;
        dbms_output.put_line('loop 밖');
end;
/

declare
 dan number := 3;
begin
    FOR i IN 1..9
    loop
        DBMS_OUTPUT.PUT_LINE(dan||'*'||i||'='||dan*i);
     end loop;
        dbms_output.put_line('loop 밖');
end;
/

declare
 dan number := 3;
begin
    FOR i IN 1..9
    loop
        continue when i=5;
            DBMS_OUTPUT.PUT_LINE(dan||'*'||i||'='||dan*i);
         end loop;
            dbms_output.put_line('loop 밖');
end;
/

##3. 게시판만들기 

주제: 인적자원시스템개발
업무파악
사원이 부서에 소속되어 근무한다.
 -장부, 시스템, 관련자들 인터뷰
모델링
 -DB : 개념적모델링 > 논리적모델링 > 물리적모델링
       Entity        정규화        table, view
       attribute     -중복최소화    컬럼,데이터,제약조건
 디자인(front)
  레이아웃,메뉴위치,색상, 
       
 프로그램 
  -class       사원
  -field     사원번호, 사원명,  급여,  부서명,  주소, email
  -method()  입력 ,수정, 삭제, 조회하다. 특정사원조회.
 
  -class       부서
  -field     부서번호, 부서이름, 소재지
  -method    입력 ,수정, 삭제, 조회하다. 특정부조회.
 
  설계 > 구현 > 통합 > 테스트 > 배포
 -----------------------------------------
 업무파악 > 요구사항 정의
 - 관리자는 글을 입력할 수 있어야 한다.
 - 관리자는 글을 수정할 수 있어야 한다.
 - 관리자는 글을 삭제할 수 있어야 한다.
 - 관리자는 글목록을 조회할 수 있어야 한다.
 - 관리자는 글상세를 조회할 수 있어야 한다.
 
 - 일반사용자는 글목록을 조회할 수 있어야 한다.
 - 일반사용자는 글상세를 조회할 수 있어야 한다.
 
 - 회원은   ~~
 - 비회원은 ~~
 
 회원가입, 로그인/로그아웃, 비번찾기, id찾기, 비번변경.... 
 
 
 -------------------------------------------------------------
 conn scott/tiger
  -- 테이블 삭제
      drop table 테이블명;
  
  -- 테이블 생성
      create table 테이블명(
      컬럼명 데이터타입(크기) default 기본값,
      컬럼명 데이터타입(크기) constraint 타입_테이블명_컬럼명 제약조건,
      컬럼명 데이터타입(크기) constraint 타입_테이블명_컬럼명 제약조건,
      컬럼명 데이터타입(크기) constraint 타입_테이블명_컬럼명 제약조건,
      컬럼명 데이터타입(크기) constraint 타입_테이블명_컬럼명 제약조건,
      );
  
  -- 더미데이터 생성
      insert into 테이블명 values();
      
      
    --------------------------------------------------------
   ##. 실습
    
  -- scott 접속
    conn scott/tiger
    
  -- 테이블 삭제
      drop table noticeboard;
  
  -- 테이블 생성
      create  table noticeboard(
      nbno     number(4)               constraint pk_notic_nbno primary key, 
      title    varchar2(120)           constraint nn_notic_title not null ,
      content  varchar2(2000)          constraint nn_notic_content not null,
      cre_date date                    default    sysdate,
      writer   varchar2(30),
      rcnt     number(5) default 0     constraint ck_notic_rcnt check(rcnt>=0), --조회수
      empno    number(5)            constraint fk_notic_empno references emp(empno)
      );
  
  -- 시퀀스 삭제
      drop sequence notice_seq;
    
  -- 시퀀스 생성
      create sequence notice_seq
      start with 1
      maxvalue 9999
      increment by 1 
      nocycle;
     
  
  -- 더미데이터 생성
      insert into noticeboard(nbno,title,content,cre_date,writer,rcnt,empno) 
      values(notice_seq.nextval,'' ,'' ,sysdate-10 ,'관리자',7900 );
      
      insert into noticeboard(nbno,title,content,cre_date,writer,rcnt,empno) 
      values(notice_seq.nextval,'' ,'' ,sysdate-10 ,'관리자',7900 );
      
      insert into noticeboard(nbno,title,content,cre_date,writer,rcnt,empno) 
      values(notice_seq.nextval,'' ,'' ,sysdate-10 ,'관리자',7900 );
      
      insert into noticeboard(nbno,title,content,cre_date,writer,rcnt,empno) 
      values(notice_seq.nextval,'' ,'' ,sysdate-10 ,'관리자',7900 );
      
      insert into noticeboard(nbno,title,content,cre_date,writer,rcnt,empno) 
      values(notice_seq.nextval,'' ,'' ,sysdate-10 ,'관리자',7900 );
      
 
 
