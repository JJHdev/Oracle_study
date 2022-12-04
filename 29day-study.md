## 1. DB연동하기. 클래스로 만들기.
```
package mystudy1;

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

## 2. 게시판 만들기
#### 오라클 디벨로퍼
```
package mystudy1;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Date;

//DAO:Data Access Object.
//DB연동하여 DB작업 제공하는 클래스이다
//이 클래스는 Board관련 DB작업 제공하는 클래스
public class NoticeBoardDAO {
	//field
	//constructor
	//method 
	//1.목록조회  public 게시글목록 getNoticeList() {
	public void getNoticeList() {
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		System.out.println("getNoticeList()진입");
		String sql = "select nbno,title,cre_date,writer,rcnt,empno " + 
					 "from noticeboard " + 
					 "order by nbno desc";
		
		//1.드라이버등록->2.커넥션얻기->3.객체준비->4.실행->5.반납
		//1번, 2번
		conn = Jdbcutil.getConnection();//1.드라이버등록&2.커넥션얻기
		
		//3번
		try {
			stmt = conn.prepareStatement(sql);
			
		//4번
			rs = stmt.executeQuery();
//		rs.get데이터타입(select순서)
//		rs.get데이터타입("컬럼명")
		
		while(rs.next()) {
			int nbno = rs.getInt("nbno");
			String title = rs.getString("title");
			Date cre_date = rs.getDate("cre_date");	
			String writer = rs.getString("writer");
			
			//printf에서 %t는 Date객체
			//%tY는 년도 %tm는 월 %td는 일
			//%tH는 년도 %tM는 분 %ts는 초
			
			System.out.printf("%5d\t%s\t%s\t%s",nbno,title,cre_date,writer);
//			System.out.printf("%5d\t%s\t%tY.%tm.%td\t%s",nbno,title,cre_date,cre_date,cre_date,writer);
			System.out.println();//줄바꿈
			
			}//while의 끝
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//5.반납
		Jdbcutil.close(rs);
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);
	}//getNoticeList()끝
	
	//2.상세조회  public 게시글 getNotice(매개변수리스트) {
	public void getNotice(int nbno) { //int nbno 글번호
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		System.out.println("getNotice()진입");
		String sql = "select nbno,title,contant,cre_date,writer,rcnt,empno " + 
					 "from noticeboard " + 
					 "where nbno=?";
		
		//1.드라이버등록->2.커넥션얻기->3.객체준비->4.실행->5.반납
		//1번, 2번
		conn = Jdbcutil.getConnection();//1.드라이버등록&2.커넥션얻기
		//3번
		try {
			stmt = conn.prepareStatement(sql);
			stmt.setInt(1, nbno);
		//4.실행
			rs = stmt.executeQuery();
//		rs.get데이터타입(select순서)
//		rs.get데이터타입("컬럼명")
		
		while( rs.next() ) {
			int nbno1 = rs.getInt("nbno");
			String title = rs.getString("title");
			String contant = rs.getString("contant");
			Date cre_date = rs.getDate("cre_date");	
			String writer = rs.getString("writer");
			int rcnt = rs.getInt("rcnt");
			int empno = rs.getInt("empno");
			
			
			//printf에서 %t는 Date객체
			//%tY는 년도 %tm는 월 %td는 일
			//%tH는 년도 %tM는 분 %ts는 초
			System.out.printf("%5d\t%s\t%s\t%s\t%s\t%d\t%s",nbno1,title,contant,cre_date,writer,rcnt,empno);
//			System.out.printf("%5d\t%s\t%tY.%tm.%td\t%s",nbno,title,cre_date,cre_date,cre_date,writer);
			System.out.println();//줄바꿈
			
			}//while의 끝
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//5.반납
		Jdbcutil.close(rs);
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);
				
	}//getNotice 끝
	
	//3.등록  -title제목, contant내용, 작성자명writer
	public void addNotice(String title,String contant,String writer) {
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		System.out.println("addNotice()진입");
		
		String sql = "INSERT INTO noticeboard(nbno,title,contant,cre_date,writer,rcnt,empno) "+ 
					 "VALUES(notice_seq.nextval,?,?,SYSDATE,?,0,7900)";
		
		//1.드라이버등록->2.커넥션얻기->3.객체준비->4.실행->5.반납
		//db연결테스트
		conn = Jdbcutil.getConnection();//1.드라이버등록&2.커넥션얻기
		
		//3.객체준비
		try {
			stmt = conn.prepareStatement(sql);
			stmt.setString(1, title);
			stmt.setString(2, contant);
			stmt.setString(3, writer);
			
		//4.실행
			stmt.executeUpdate();
			
			//printf에서 %t는 Date객체
			//%tY는 년도 %tm는 월 %td는 일
			//%tH는 년도 %tM는 분 %ts는 초
			System.out.printf("addNotice() title:%s, contant:%s, writer:%s\r\n",title,contant,writer );
			System.out.println();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//5.반납
		Jdbcutil.close(rs);
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);
}
	
	//4.수정  -nbno글번호, title제목, contant내용, 작성자명writer
	public void updateNotice(int nbno, String title,String contant,String writer) {
		
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		String sql = "update noticeboard "+ 
					 "set title=?,contant=?,writer=? "+ 
					 "where nbno=?";
					
		//1.드라이버등록->2.커넥션얻기->3.객체준비->4.실행->5.반납
		//db연결테스트
		conn = Jdbcutil.getConnection();//1.드라이버등록&2.커넥션얻기
		//3.객체준비
		try {
			stmt = conn.prepareStatement(sql);
			stmt.setString(1, title);
			stmt.setString(2, contant);
			stmt.setString(3, writer);
			stmt.setInt(4, nbno);
			
		//4.실행
			int resultCnt = stmt.executeUpdate();
			System.out.println("쿼리문실행결과로 받은 record수="+resultCnt);
			
			
			//printf에서 %t는 Date객체
			//%tY는 년도 %tm는 월 %td는 일
			//%tH는 년도 %tM는 분 %ts는 초
			System.out.printf("updateNotice() nbno:%d, title:%s, contant:%s, writer:%s\r\n",nbno,title,contant,writer);
			System.out.println();//줄바꿈
			
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//5.반납
		Jdbcutil.close(rs);
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);

}	
	//5.삭제 
	public void delNotice(int nbno) {
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		String sql = "delete noticeboard " + 
				     "where nbno=?";
					
		//1.드라이버등록->2.커넥션얻기->3.객체준비->4.실행->5.반납
		//db연결테스트
		conn = Jdbcutil.getConnection();//1.드라이버등록&2.커넥션얻기
		//3.객체준비
		try {
			stmt = conn.prepareStatement(sql);
			stmt.setInt(1, nbno);
			
		//4.실행
			stmt.executeUpdate();
			System.out.printf("delNotice() nbno:%d",nbno);
			System.out.println();//줄바꿈
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//5.반납
		Jdbcutil.close(rs);
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);
	}
		
}
```
## 3. 실행클래스 만들기
```
package mystudy1;

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
					noticeBoardDAO.getNoticeList();
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
					System.out.print("글번호는?:");
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


