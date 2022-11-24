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
 
 ★★★★★
 4. Oracle과 Java DM 연결
  - JDBC Driver 설치 (로컬pc에서 oracle 설치하면 있음)
  - eclipse 들어간 후 pro1 오른쪽클릭 > build path > configure build path > liberary > add > C:\oraclexe\app\oracle\product\11.2.0\server\jdbc\lib > ojdbc6선택 후  apply and close
  - 사용package 만들기(강의에서는 db) > 클래스만들기(main method o) 
  - DB 연결 메소드 호출(주소참조)
  -  에러가 tns%로 되 > 작업관리자 > 서비스 > oracleServiceXE and OracleXETNSListner 실행중으로 바꾸기.


```
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

// scott의 emo태아불에 데이터입력 작업을 위한 클래스이다.
public class DbConnectionTest {
	public static void main(String[] args) {
		
		String url = "jdbc:oracle:thin:@localhost:1521/xe"; 
		//localhost는 ip주소 127.0.0.1로 써도 된다. xe는 오라클의 버전에 따라 달라진다. 확인방법은 : 오라클 사용시 파일 만들때 SID 번호있음.
		String user = "scott";
		String password = "tiger";
		
//1. JDBC Driver등록
		try {
			Class.forName("oracle.jdbc.OracleDriver");
		} catch (ClassNotFoundException e) {
			System.out.println("ClassNotFoundException 발생");
			e.printStackTrace();
		}
		System.out.println("1. JDBC Driber등록-성공");
		
		
//2. 연결 Connection얻기 (DriverManager클래스에서 Connection 구현객체 생성)
//DriverManager.getConnection(연결DB서버(url), 접속계정(user), password)
		Connection conn =null;
		try {
			conn = DriverManager.getConnection(url, user, password);
		} catch (SQLException e) {
			System.out.println("SQLException 발생");
			e.printStackTrace();
		}	
		System.out.println("2. connection succes");
		
//3. QUERY실행조작을 위한 준비조작(객체준비 -Statement객체, PreparedStatement객체)
// - QUERY문에 ;미포함, 작은따옴표,공백,괄호 등등 주의 → 실행가능한 쿼리문 !
// - ""+""처럼 절로 묶어주는 이유는 붙어있으면 앞뒤로 같은단어로 인식하고 보기 어렵다. 
		String sql ="insert into emp (empno,ename,job ,hiredate,sal,comm)" + 
			     	"values (9004,'손흥민','매니져',sysdate, 3000,1500)";
			Statement stmt = null;
		try {
			stmt = conn.createStatement();
			conn.createStatement();
		} catch (SQLException e1) {
			e1.printStackTrace();
		}
		
//4. QUERY실행
//executeUpdate()메소드를 호출하면		
//주어진 SQL문 DML,DDL(insert,update,delete)이 실행되고
//실행된 레코드수가 리턴된다.
//executeQuery(select문) : select쿼리문실행. 실행결과를 Resultset한다.
		try {
			int resultCnt = stmt.executeUpdate(sql);
			System.out.println("QUERY실행결과로 받은 record수 ="+resultCnt);
		} catch (SQLException e1) {
			System.out.println("executeUpdate()실행관련 에러");
			e1.printStackTrace();
		}
		
//5. QUERY종료(자원반납,해제)
		try {
			if(stmt != null) {
				stmt.close();
			}
			if(conn != null) {
				conn.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		  }
		System.out.println("5. 자원반납 진행");
		}
	}
```
