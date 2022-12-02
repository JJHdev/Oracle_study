## DB연동하기. (메소드로 처리)
```
package db.ex2;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

// DB연결을 위한 클래스이다.
// jdbc driver연결
public class Jdbcutil {
	
	//field
	static String url = "jdbc:oracle:thin:@localhost:1521/xe"; 
	static String user = "scott";
	static String password = "tiger";
	static Connection conn =null;
	
	//constructor
	
	
	//method
		public static Connection getConnection() {
		try {
			//1. JDBC Driver등록
			Class.forName("oracle.jdbc.OracleDriver");
			//2. 연결 Connection얻기 (DriverManager클래스에서 Connection 구현객체 생성)
			conn = DriverManager.getConnection(url, user, password);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}	
		System.out.println("1. JDBC Driber등록-성공,connection succes");
		return conn;
	}
	
//5. 자원반납
		public static void close(Statement stmt) {
			try {
				if (stmt != null){stmt.close();}
			} catch (SQLException e) {
				e.printStackTrace();
			}
			System.out.println("5. 자원반납");
		}
	
//5. 접속종료
		public static void close() {
			try {if (conn != null)conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
			System.out.println("5. 접속종료");
		}
	}

```

## 2. 게시판 만들기
#### 오라클 디벨로퍼
 --1.목록조회-최신글부터 출력   
select nbno,title,cre_date,writer
from noticeboard
order by nbno asc;
   
--2.상세조회-특정글번호
SELECT nbno,title,contant,cre_date,writer,rcnt,empno
FROM noticeboard
WHERE nbno = 1;


--3.등록--여기에서 작성자id는 7900으로 임시고정(별도의 작업이 필요하므로)
INSERT INTO noticeboard(nbno,title,contant,cre_date,writer,rcnt,empno)    
VALUES(notice_seq.nextval,'?제목1','?내용1',SYSDATE,'?',0,7900);


--4.수정-특정글번호의 제목,내용,작성자    
update noticeboard 
set title='제목1',contant='내용1',writer='관리자'  
where nbno=1;
commit;
--5.삭제-특정글번호
delete from noticeboard
where nbno=1;

## 자바입력
####1. DB연동 클래스 만들기.
```
package db.ex2;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

// DB연결을 위한 클래스이다.
// jdbc driver연결
public class Jdbcutil {
	
	//field
	static String url = "jdbc:oracle:thin:@localhost:1521/xe"; 
	static String user = "scott";
	static String password = "tiger";
	//constructor
	
	
	//method
		public static Connection getConnection() {
			Connection conn =null;
		try {
			//1. JDBC Driver등록
			Class.forName("oracle.jdbc.OracleDriver");
			//2. 연결 Connection얻기 (DriverManager클래스에서 Connection 구현객체 생성)
			conn = DriverManager.getConnection(url, user, password);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}	
		return conn;
	}
	
//5. 자원반납
		public static void close(Statement stmt) {
			try {
				if (stmt != null){stmt.close();}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	
//5. 접속종료
		public static void close(Connection conn) {
			try {if (conn != null)conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		
		public static void close(ResultSet rs) {
			try {if (rs != null)rs.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
	}
}
```
####2. noticeboard 클래스 만들기.
```
package db.ex2;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Date;

//DAO : data에 접근하기 위한 객체로 데이터베이스 접근위한 로직과 비즈니스 로직을 분리하기위함.
//이 클래스는 DB하고 연동하여 DML기능을 제공하는 클래스이다.
public class NoticeBoardDAO {
	//field
	//constructor
	//method
	
	//"1목록조회"
	public void getNoticeList(int nbno12) {
		PreparedStatement stmt = null;
		Connection conn = null ;
		ResultSet rs = null ;
		String sql = "select nbno,title,cre_date,writer "+
					 "from noticeboard "+
				     "order by nbno asc ";
		conn = Jdbcutil.getConnection();
		//3. 준비
			try {
				stmt = conn.prepareStatement(sql);
				rs = stmt.executeQuery();
		//4. 실행
				//rs.get데이터타입 (select순서)
				//rs.get데이터타입 ("컬럼명")
				while(rs.next()) {
					int nbno = rs.getInt("nbno");
					String title = rs.getString("title");
					Date cre_date = rs.getDate("cre_date");
					String writer = rs.getString("writer");
					
				//printf에서 %t는 date객체 %ty는 년도 %tm는 월  %td는 일 %tH는 시간 %tM는 분 %ts는 초
					System.out.printf("%5d,\t%s,\t%s,\t%s,\t",nbno,title,cre_date,writer );
					System.out.println("");
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
			
		//db연결 종료
			Jdbcutil.close(stmt);
			Jdbcutil.close(conn);
			Jdbcutil.close(rs);
	}//NoticeBoardDAO의 끝
	
	
	
	
	//"2특정상세조회 "
	public void getNotice(int nbno123) {
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		String sql = "SELECT nbno,title,contant,cre_date,writer,rcnt,empno "+
					 "FROM noticeboard "+
					 "WHERE nbno = ?";
		
		conn = Jdbcutil.getConnection();
		
		try {
			stmt = conn.prepareStatement(sql);
			rs = stmt.executeQuery();
			
			while(rs.next()) {
				int nbno = rs.getInt("nbno");
				System.out.printf("%d",nbno);
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//db연결 종료
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);
		Jdbcutil.close(rs);
		
		
		
		
	}
	//"3입력"
	public void addNotice(String title,String content,String writer) {
		System.out.printf("addNotice() title: %s, content: %s, writer: %s \r\n",title,content, writer);
		System.out.println("");
		Jdbcutil.getConnection();
	}
	//"4수정"
	public void updateNotice(int nbno, String title,String content,String writer) {
		System.out.printf("updateNotice() nbno: %d, title: %s, content: %s, writer: %s \r\n",nbno,title,content, writer);
		System.out.println("");
		Jdbcutil.getConnection();
		
	}
	//"5삭제"
	public void delNotice(int nbno) {
		System.out.printf("delNotice() nbno: %d \r\n",nbno);
		System.out.println("");
		Jdbcutil.getConnection();
		
	}
}
```
####3. 실행클래스 만들기
```
package db.ex2;

import java.util.Scanner;

//시작 메인클래스.
//web에서는 불필요한 부분 
public class Main {

	public static void main(String[] args) {
		
		Scanner scanner= new Scanner(System.in);
		NoticeBoardDAO noticeBoardDAO = new NoticeBoardDAO();
		
		
		while (true) {
					System.out.println("----1목록조회  2특정상세조회  3입력  4수정  5삭제  6종료                  ");
				int num = scanner.nextInt();//다음 입력되어있는 토큰은 순서에 따라 정수로 받아들인다.
					
				if(num == 1) {
					System.out.println("조회할 글 번호");
					int nbno = scanner.nextInt();
					noticeBoardDAO.getNoticeList(nbno);
				}else if(num == 2){
					System.out.println("조회할 글 번호");
					int nbno = scanner.nextInt();
					noticeBoardDAO.getNotice(nbno);
				}else if(num == 3){
					System.out.print("제목써라:");
					String title = scanner.next();
					System.out.print("내용써라:");
					String content = scanner.next();
					System.out.print("누가썻냐:");
					String write = scanner.next();
					noticeBoardDAO.addNotice(title,content,write);
				}else if(num == 4){
					System.out.print("제목써라:");
					String title = scanner.next();
					System.out.print("내용써라:");
					String content = scanner.next();
					System.out.print("누가썻냐:");
					String write = scanner.next();
					System.out.print("누가썻냐:");
					int nbno = scanner.nextInt();
					noticeBoardDAO.updateNotice(nbno,title,content,write);
				}else if(num == 5){
					System.out.println("삭제할 글 번호");
					int nbno = scanner.nextInt();
					noticeBoardDAO.delNotice(nbno);
				}else if(num == 6){
					break;
				}else {
					System.out.println("잘못된 번호.");
				}								
				
		}
			/*//db연결테스트
			Jdbcutil.getConnection();
			//db연결 종료
			Jdbcutil.close();*/
		
	}
}
```


