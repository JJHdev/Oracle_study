### UPDATE 문
 - UPDATE [테이블] SET [열] = '변경할값' WHERE [조건]
 
### Update문을 자바에 적용하기.
```
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

//DB연결을 위한 클래스이다.
public class UpdateEx01 {
	public static void main(String[] args) {
		
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
		String url = "jdbc:oracle:thin:@localhost:1521/xe"; //localhost는 ip주소 127.0.0.1로 써도 된다. xe는 오라클의 버전에 따라 달라진다. 확인방법은 : 오라클 사용시 파일 만들때 SID 번호있음.
		String user = "scott";
		String password = "tiger";
		Connection conn =null;
		PreparedStatement stmt = null;
				
		try {
			conn = DriverManager.getConnection(url, user, password);
		} catch (SQLException e) {
			System.out.println("SQLException 발생");
			e.printStackTrace();
		}	
		System.out.println("2. connection succes");
		
//3. QUERY실행조작을 위한 준비조작(객체준비 -Statement객체, PreparedStatement객체)
		String sql =
				"UPDATE EMP "+ 
				"SET JOB=?, SAL=? , DEPTNO=? "+ 
				"WHERE EMPNO =?";

		try {
			stmt = conn.prepareStatement(sql);
		} catch (SQLException e1) {
			e1.printStackTrace();
		}

//4. QUERY실행
		try {
			stmt.setString(1,"개발");
			stmt.setInt(2, 5000);
			stmt.setInt(3, 97);
			stmt.setInt(4, 9003);
			int resultCnt = stmt.executeUpdate();
			System.out.println("retuen tn ="+resultCnt);
		} catch (SQLException e1) {
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
 - executeUpdate() : insert, update, delete
 - executeQuery() : select
 
 
 
### Update문을 자바에 적용하기.

rs.get데이터타입(Select 컬럼순서) 첫번째 컬럼은 1, 2번째 컬럼은 2...
rs.get데이터타입 ("컬럼명 or 컬럼별칭")

```
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Date;

//DB연결을 위한 클래스이다.
//preparedStatement객체를 이용한 select문 실행
public class SelectEx02 {
	public static void main(String[] args) {
		
		String url = "jdbc:oracle:thin:@localhost:1521/xe"; //localhost는 ip주소 127.0.0.1로 써도 된다. xe는 오라클의 버전에 따라 달라진다. 확인방법은 : 오라클 사용시 파일 만들때 SID 번호있음.
		String user = "scott";
		String password = "tiger";
		Connection conn =null;
		PreparedStatement stmt = null;
		ResultSet rs = null;
		
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
		try {
			conn = DriverManager.getConnection(url, user, password);
		} catch (SQLException e) {
			System.out.println("SQLException 발생");
			e.printStackTrace();
		}	
		System.out.println("2. connection succes");
		
//3. QUERY실행조작을 위한 준비조작(객체준비 -Statement객체, PreparedStatement객체)
		String sql =
				"SELECT empno,ename,job,hiredate,sal,deptno "+
				"FROM EMP ";
		try {
			stmt = conn.prepareStatement(sql);
		} catch (SQLException e1) {
			e1.printStackTrace();
		}

//4. QUERY실행
		try {
			rs = stmt.executeQuery();
				//rs.get데이터타입(Select 컬럼순서) 첫번째 컬럼은 1, 2번째 컬럼은 2...
				//rs.get데이터타입 ("컬럼명 or 컬럼별칭")
				//int eno = rs.getInt("1");
				while(rs.next()) {
					int eno = rs.getInt("empno");
					int deptno = rs.getInt("deptno");
					String ename = rs.getString("ename");
					String job = rs.getString("job");
					Date hiredate = rs.getDate("hiredate");
					
					System.out.printf("%6d/ %5s, %8s는 \r\n",eno,ename,job); // d = 숫자, s=문자.
					System.out.println("          "); //줄바꿈
					//System.out.print(hiredate);
					//System.out.print(deptno);
				}
				//if(rs.next()) { //rs.next()는 다음행이 있으면 true 리턴
				//System.out.println("select 결과있어요");
				//}else {
				//System.out.println("select 결과없어요");
				//}
				System.out.println("4번 retuen tn ="+rs);
		} 
		
		catch (SQLException e1) {
			e1.printStackTrace();
		}
		
		
//5. QUERY종료(자원반납,해제)
		try {
			if(rs != null) {
				rs.close();
			}
			
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

### DML문을 자바에 적용하기.
 - 한 클래스안에 메인메소드, 메소드로 DB자료 구현.

 
```
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Date;

//scott.emp에 DML작업
public class DMLex02 {
	//필드
	String url ="jdbc:oracle:thin:@127.0.0.1:1521/xe";
	String user="scott";
	String password="tiger";
	Connection conn=null;
	//Statement  stmt=null;
	PreparedStatement stmt=null;
	
	//생성자 
	public DMLex02(){} //기본생성자-매개변수가 없는 생성자
	
	//메서드
	
	//1.JDBC Driver등록 &	2.연결 Connection얻기
	public void dbConnection() {
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
		
	}
	
	
	public static void main(String[] args) {

		
		//객체생성
		DMLex02 obj=new DMLex02();
		
		//1.JDBC Driver등록&	2.연결 Connection얻기
		obj.dbConnection();
		
		//3.객체준비 & 4.쿼리실행
		//사원입사->사원목록조회->사원삭제->사원목록조회
		
		System.out.println();//빈줄
		System.out.println("--아래는 사원입사----");
		obj.insertEmp(9012,"김민재","사원",2789,1234);//사원입사
		
		System.out.println();//빈줄
		System.out.println("--아래는 사원목록조회----------------------");
		obj.selectEmpList();//사원목록조회
		
		//사원정보수정
		//특정사원조회
		
		System.out.println();//빈줄
		System.out.println("--아래는 사원삭제----");
		obj.deleteEmp(9012);// 사번이 9012인 사원삭제
		
		System.out.println();//빈줄
		System.out.println("--아래는 사원목록조회----------------------");		
		obj.selectEmpList();//사원목록조회
		

	}//main
	
	//자원반납
	//다형성 적용시켜보기
	//상위클래스명 참조변수=new 하위클래스명();
	//인터페이스명 참조변수=new 구현클래스명();
	//자원해제-Statement,PreparedStatement 
	public void close(Object stmt) {
		try {
			if(stmt!=null) {
				if(stmt  instanceof Statement) {
					((Statement)stmt).close();
				}
				
				if(stmt  instanceof PreparedStatement) {
					((PreparedStatement)stmt).close();
				}
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.자원반납완료");
	}//close()끝
	
	//Connection자원해제
	public void close(Connection conn) {
		try {
			if(conn!=null) {
				conn.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.자원반납완료");
	}//close()끝
	
	
	//입력
	public void insertEmp(int empNo,String name,String job,int pay,int commission) {
		System.out.printf("insertEmp()진입 empNo:%d, name:%s, job:%s, pay:%d, 커미션:%d\r",empNo,name,job,pay,commission);
		String sql = 
				"INSERT INTO emp(empno,ename,job,hiredate,sal,comm) "+ 
				"VALUES(?,?,?,SYSDATE,?,?)";

		try {
			stmt =conn.prepareStatement(sql);
	
			stmt.setInt(1, empNo);
			stmt.setString(2,name);
			stmt.setString(3,job);
			stmt.setInt(4, pay);
			stmt.setInt(5, commission);
			int resultCnt = stmt.executeUpdate();
			System.out.println("4.쿼리문실행결과로 받은 record수="+resultCnt);
		} catch (SQLException e1) {
			System.out.println("executeUpdate()실행관련 에러");
			e1.printStackTrace();
		}
		
		try {
			if(stmt!=null) {
				stmt.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.insertEmp()의 stmt자원반납");
	}//insertEmp()끝
	
	//수정
	//public void updateEmp(매개변수) {}
	//삭제
	public void deleteEmp(int empno) { 
		System.out.println("deleteEmp()진입 empno="+empno); 
		
		//3.객체준비-PreparedStatement객체
		String sql = 
				"DELETE FROM emp " + 
				"WHERE empno=?";

		try {
			stmt =conn.prepareStatement(sql);
		} catch (SQLException e1) {
			e1.printStackTrace();
		}
		System.out.println("3.PreparedStatement객체받기");
		
		//4.쿼리실행
		try {
			stmt.setInt(1,empno);
			int resultCnt = stmt.executeUpdate();
			System.out.println("4.쿼리문실행결과로 받은 record수="+resultCnt);
		} catch (SQLException e1) {
			System.out.println("executeUpdate()실행관련 에러");
			e1.printStackTrace();
		}
		
		//5.자원해제
		try {
			stmt.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.deleteEmp()의 stmt자원반납");
	}//deleteEmp()끝
	
	
	
	
	
	
	//사원목록조회
	public void selectEmpList() {
		System.out.println("selectEmpList()진입");
		
		ResultSet rs = null;
		String sql = "SELECT empno,ename,job,hiredate,sal"+
					 " FROM EMP";
		//3.객체준비-PreparedStatement객체
		try {
			stmt =conn.prepareStatement(sql);
			System.out.println("3.PreparedStatement객체 생성");
			
			//4.쿼리실행
			rs = stmt.executeQuery();
			System.out.println("4.쿼리문실행결과 : ");

			while( rs.next() ) { //rs.next()는 다음행(new row)있으면 true리턴
				int eno=rs.getInt("empno");//empno컬럼의 값을 자바에서 int로 처리
				String eName=rs.getString("ename");//job컬럼값 가져오기
				String job=rs.getString("job");//ename컬럼값 가져오기
				Date hireDate=rs.getDate("hiredate");//hiredate컬럼값 가져오기
				String strHireDate = rs.getString("hiredate");
				double salary=rs.getDouble("sal");//sal컬럼값 가져오기
				
				System.out.printf("%10d%10s, %10s는 ", eno, eName, job);
				System.out.print(hireDate);
				System.out.print(strHireDate);
				System.out.print(salary);
				System.out.println();//줄바꿈
			}
			
			
		} catch (SQLException e1) {
			System.out.println("executeUpdate()실행관련 에러");
			e1.printStackTrace();
		}
		
		//5.자원반납
		try {
			if(rs!=null) {	
				rs.close();
			}
			
			if(stmt!=null) {
				stmt.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("5.selectEmpList-자원반납완료");
	}//selectEmpList()끝
	
	//특정사원조회
	//public void selectEmp(매개변수) {}
	
	
}
```

