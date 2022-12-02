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

#### 자바입력
1. 



