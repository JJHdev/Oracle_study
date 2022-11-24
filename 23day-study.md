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


# DB연결을 위한 클래스이다
```
package db; 

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

//DB연결을 위한 클래스이다
public class DBConnectionTest {

	public static void main(String[] args) {
		String url ="jdbc:oracle:thin:@127.0.0.1:1521/xe";
		String user="scott";
		String password="tiger";
		Connection conn=null;
		
		//1.JDBC Driver등록
		try {
			Class.forName("oracle.jdbc.OracleDriver");
		} catch (ClassNotFoundException e) {
			System.out.println("ClassNotFoundException발생 e="+e);
			e.printStackTrace();
		}
		System.out.println("1.JDBC Driver등록-성공");
		
		//2.연결 Connection얻기
		//DriverManager.getConnection(연결DB서버url, 접속user명, 비밀번호)

		try {
			conn=DriverManager.getConnection(url, user, password);
		} catch (SQLException e) {
			System.out.println("SQLException발생");
			e.printStackTrace();
		}
		System.out.println("2.Connection얻기-성공");
		
		//3.객체준비-Statement객체,PreparedStatement객체
		
		//4.쿼리실행
		
		//5.자원반납
		try {
			if(conn!=null) {
				conn.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.자원반납완료");
	}

}

```

# scott의 emo테이블에 데이터입력 작업을 위한 클래스이다.
```
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

// scott의 emo테이블에 데이터입력 작업을 위한 클래스이다.
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
			Statement stmt = null;
		try {
			stmt = conn.createStatement();
			conn.createStatement();
		} catch (SQLException e1) {
			e1.printStackTrace();
		}
		
//4. QUERY실행
// - QUERY문에 ;미포함, 작은따옴표,공백,괄호 등등 주의 → 실행가능한 쿼리문 !
// - ""+""처럼 절로 묶어주는 이유는 붙어있으면 앞뒤로 같은단어로 인식하고 보기 어렵다. 
		int eno = 9004;
		int sal = 3000;
		double comm = 1500;
// - oracle에서는 문자열에는 ' ' 로 묶어주니 " ' ' " 로 묶어줘야한다.
		String enpname = "'이강인'";
		String job = "매니져";
		String sql ="insert into emp (empno,ename,job ,hiredate,sal,comm)" + 
				"values ("+eno+","+enpname+",'"+job+"',sysdate,"+sal+","+comm+")";
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

# prepareStatement 문법 사용하는 클래스
```
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

// scott의 emo태아불에 데이터입력 작업을 위한 클래스이다.
// prepareStatement 문법 사용하는 클래스.
public class InsertEx02 {
	public static void main(String[] args) {
		
		String url = "jdbc:oracle:thin:@localhost:1521/xe"; 
		String user = "scott";
		String password = "tiger";
		PreparedStatement stmt = null;
		
//1. JDBC Driver등록
		try {
			Class.forName("oracle.jdbc.OracleDriver");
		} catch (ClassNotFoundException e) {
			System.out.println("ClassNotFoundException 발생");
			e.printStackTrace();
		}
		System.out.println("1. JDBC Driber등록-성공");
		
		
//2. 연결 Connection얻기 (DriverManager클래스에서 Connection 구현객체 생성)
		Connection conn =null;
		try {
			conn = DriverManager.getConnection(url, user, password);
		} catch (SQLException e) {
			System.out.println("SQLException 발생");
			e.printStackTrace();
		}	
		System.out.println("2. connection succes");
		
//3. QUERY실행조작을 위한 준비조작(객체준비 -Statement객체, PreparedStatement객체)
		String sql ="insert into emp (empno,ename,job ,hiredate,sal,comm)" + 
					"values (?,?,?,sysdate,?,?)";
		try {
			stmt= conn.prepareStatement(sql);
		} catch (SQLException e1) {
			e1.printStackTrace();
		}
		
//4. QUERY실행
		//PreparedStatement객체를 실행하기전에  (executeUpdate()혹은 executeQuery())
//?개수만큼 set설정해야한다
//set데이터타입(?순서,값)
		try {
			stmt.setInt(1, 9004);
			stmt.setString(2, "이강인");
			stmt.setString(3, "매니져");
			stmt.setInt(4, 3000);
			stmt.setInt(5, 1500);
			int resultCnt = stmt.executeUpdate();
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

# scott의 emp테이블에 데이터삭제 작업을 위한 클래스이다
```
package db; 

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;

//scott의 emp테이블에 데이터삭제 작업을 위한 클래스이다
//PreparedStatement객체를 이용한 delete문 실행 
public class DeleteEx {

	public static void main(String[] args) {
		String url ="jdbc:oracle:thin:@127.0.0.1:1521/xe";
		String user="scott";
		String password="tiger";
		Connection conn=null;
		//Statement  stmt=null;
		PreparedStatement stmt=null;
		
		//1.JDBC Driver등록
		try {
			Class.forName("oracle.jdbc.OracleDriver");
		} catch (ClassNotFoundException e) {
			System.out.println("ClassNotFoundException발생 e="+e);
			e.printStackTrace();
		}
		System.out.println("1.JDBC Driver등록-성공");
		
		//2.연결 Connection얻기
		//DriverManager.getConnection(연결DB서버url, 접속user명, 비밀번호)

		try {
			conn=DriverManager.getConnection(url, user, password);
		} catch (SQLException e) {
			System.out.println("SQLException발생");
			e.printStackTrace();
		}
		System.out.println("2.Connection얻기-성공");
		
		//3.객체준비-PreparedStatement객체
		//An object that represents a precompiled SQL statement. 
		String sql = 
				"DELETE FROM emp " + 
				"WHERE empno=?";

		try {
			//stmt = conn.createStatement();
			stmt =conn.prepareStatement(sql);
		} catch (SQLException e1) {
			e1.printStackTrace();
		}
		
		//4.쿼리실행
		//query문에 ;미포함, 작은따옴표,공백,괄호 등등 주의=> 실행가능한 쿼리문!			
		//executeUpdate() 메소드를 호출하면 
		//주어진 SQL문(insert,update,delete)이 실행. 실행된 레코드수가 리턴된다
		//executeQuery(select문) : select쿼리문실행. 실행결과를 ResultSet리턴
		try {
			//int resultCnt = stmt.executeUpdate(sql);
			
			//PreparedStatement객체를 
			//실행하기전에(executeUpdate() 혹은 executeQuery() )
			//?개수만큼 set설정해야한다
			//set데이터타입(?순서,값)
			stmt.setInt(1, 9004);
			int resultCnt = stmt.executeUpdate();
			System.out.println("쿼리문실행결과로 받은 record수="+resultCnt);
		} catch (SQLException e1) {
			System.out.println("executeUpdate()실행관련 에러");
			e1.printStackTrace();
		}
		
		//5.자원반납
		try {
			if(stmt!=null) {
				stmt.close();
			}
			if(conn!=null) {
				conn.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.자원반납완료");
	}
}
```
