```
--제약조건
1)not null : null 허용x → 값을 넣어라. 필수입력
2)Primary key : pk.식별키 (not null(성격), unique(유일한 값,중복허용x))
            이면서 테이블을 대표, 각 레코드를 구분할수있어야 한다.
-- 주의 : 컬럼타입(문자&날짜는 ''로 묶어줘야 한다.)
         컬럼크기(숫자&영문 1글자는 1byte, 여기에서는(encoding설정에 따라 달라짐) 한글 1글자는 3byte)
```

```
-- 조회문법
SELECT {* | 컬럼명 [AS]}
FROM 테이블명
[where 조건]
[group by 그룹기준,...]
[havin 그룹조건]
[order by 정렬기준 정렬방법];
-- 입력문법
INSERT INTO 테이블[(컬럼명,..컬럼명)]
VALUES(값,..값);
```

1. 명시적 null 입력
 - insert into (1,2,3)
 - (95,null,'') 2번째, 3번째 null값 입력
2. 묵시적 null 입력
 - insert into (1)
 - (95) 명시하지 않으면 null값이 입력된다.
 
3. 테이블의 모든 컬럼에 값을 넣을때는 안써도 된다. 
insert into dept
values (1,2,3);
 
3. commit; 을 쳐야 DB에 DML작업내용을 반영한다.

4. 현재 날짜 조회
 - select sysdate
   from dual;
   
```
select sysdate-1, sysdate, sysdate+1
from dual;
```
5. 날짜 입력
```
 to_char(날자, '날짜포멧')
 to_char(문자, '날짜시간format')
 to_char('11/11/11','dd/mm/yy')
 년 yy
 월 mm,month
 일 dd
 시 hh
 분 mi
 초 ss
 
 시간표현 (문자열로 바꿔서 시간을 표현한다.)
select sysdate as 현재, to_char(SYSDATE+(1/24/60),'yyyy.month.dd hh:mi:ss') as "1분 뒤"
from dual;
```

★★★★★
to_number (문자)
to_char(숫자,'숫자format')
to_char(숫자,'날짜format')
to_date (문자,'날짜시간format')
to_timestamp (문자,'날짜시간format')

```
- 년.월.분으로 표현하는방법 (날짜 기준은 /로 구분되어있음)
select to_char(to_date(801123,'yymmdd'),'yy.mm.dd am')
from dual;
```


6. 수정
UPDATE 테이블 
SET 컬럼명 = 새값 ,컬럼명 = 새값 ...
[where 조건]

7. 삭제
DELETE [FROM] 테이블명
[where 조건]

9. DML(입력,수정,삭제)
