# Html 공부후 develrofer 와 java연동
## 3. arrylist로 통한 데이터입력받기.
### 3-1. 이론 내용
------------ row:1개, 컬럼:1개 ------------
[접근제한자] [제어자] 문자열유형 getEmpName(숫자타입 사원번호){ }
[접근제한자] [제어자] String        getEmpName(int        사원번호){  return ~; }
예문) 사원번호를 입력받아 사원이름조회
select ename
from emp
where empno=7782  ?;


------------ row:n개, 컬럼:1개 ------------
[접근제한자] [제어자] String[]              getEmpName(){ return~;}
[접근제한자] [제어자] List<String>        getEmpName(){ return~;}
[접근제한자] [제어자] ArrayList<String> getEmpName(){ return~;}

예문) 모든사원명단조회
select ename
from emp;



[접근제한자] [제어자] 리턴유형 메서드명(매개변수){    return ~; }
[접근제한자] [제어자] 사원DTO 메서드명(데이터타입 사원번호){    
   //리턴유형을 DTO로 넘기는 방법2가지
   //방법1.기본생성자를 이용하여 객체를 생성하는 경우
   사원DTO 참조변수=new 사원DTO();
   데이터타입 변수명=rs.get데이터타입(컬럼명);
   참조변수.set필드명(데이터타입 변수명); 
    혹은
   참조변수.set필드명( rs.get데이터타입(컬럼명)  );
  

    //방법2.파라미터가 있는 생성자를 이용하여 객체를 생성하는 경우
    사원DTO 참조변수=new 사원DTO( rs.get데이터타입(컬럼명), 
                                                  rs.get데이터타입(컬럼명), 
                                                  rs.get데이터타입(컬럼명) );
 return 참조변수;
 }


-------------------------- row:1개, 컬럼명:4개 --------------------
사원DTO 참조변수=new 사원DTO(7782,"김구","1979/10/11",3300);

사원번호를 조회받아 해당 사원의 상세정보조회하기
예문) 특정글번호상세조회, 사원정보조회, 회원정보조회, 상품상세정보
select empno,ename,hiredate,deptno
from emp
where empno=7782;

[접근제한자] [제어자] 리턴유형 메서드명(매개변수){    return ~; }
[접근제한자] [제어자] Array[] 메서드명(매개변수){    return ~; }
[접근제한자] [제어자] List<사원DTO> 메서드명(매개변수){    return ~; }
[접근제한자] [제어자] ArrayList<사원DTO> 메서드명(매개변수){   
                                                       return ArrayList<사원DTO>; }

List<사원DTO>         list = new ArrayList(); 또는
ArrayList<사원DTO>  list = new ArrayList(); 

while( rs.next ){
  사원DTO 참조변수=new 사원DTO( rs.get데이터타입(컬럼명), rs.get데이터타입(컬럼명));
   list.add(참조변수);

  사원DTO 참조변수=new 사원DTO(7788,"홍길동","1982/01/01",3000);
  사원DTO 참조변수=new 사원DTO(7790,"이순신","1982/09/15",4000);
  사원DTO 참조변수=new 사원DTO(7800,"강감찬","1988/12/25",1500);
}
 return list
    
### 3-1. 실습 내용
#### DAO 
```
package db.ex5;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

//DAO:Data Access Object.
//DB연동하여 DB작업 제공하는 클래스이다
//이 클래스는 Board관련 DB작업 제공하는 클래스
public class NoticeBoardDAO {
	//field
	//constructor
	//method 
	public List<NoticeBoardDTO> getNoticeList() {
		System.out.println("getNoticeList()진입");
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		
		//인터페이스타입 참조변수명 =new 구현클래스명();
		 List<NoticeBoardDTO> list = new ArrayList(); 
		//클래스명타입 참조변수명 =new 클래스명();
		//ArrayList<NoticeBoardDTO> list = new ArrayList(); 
		 
		
		String sql = "select nbno,title,contant,cre_date,writer,rcnt,empno " + 
					 "from noticeboard " + 
					 "order by nbno desc";
		
		conn = Jdbcutil.getConnection();//1.드라이버등록&2.커넥션얻기
		
		try {
			stmt = conn.prepareStatement(sql);
			
			rs = stmt.executeQuery();
		while(rs.next()) {
			
			int nbno = rs.getInt("nbno");
			String title = rs.getString("title");
			String contant = rs.getString("contant");
			Date cre_date = rs.getDate("cre_date");	
			String writer = rs.getString("writer");
			int rcnt = rs.getInt("rcnt");
			int empno = rs.getInt("empno");
			
		//방법1. 기본생성자를 이용하여 객체를 생성하는 경우.
			NoticeBoardDTO nbdto= new NoticeBoardDTO();
			nbdto.setNbno(nbno);
			nbdto.setTitle(title);
			nbdto.setContent(contant);
			nbdto.setCre_date(cre_date);
			nbdto.setWriter(writer);
			nbdto.setRcnt(rcnt);
			nbdto.setEmpno(empno);
			
		//공지사항게시판의 객체를 list에 추가한다.
			list.add(nbdto);
			
			/*System.out.printf("%5d\t%s\t%s\t%s",nbno,title,cre_date,writer);
			System.out.println();//줄바꿈*/
			
			}//while의 끝
		} catch (SQLException e) {
			e.printStackTrace();
		}
		//5.반납
		Jdbcutil.close(rs);
		Jdbcutil.close(stmt);
		Jdbcutil.close(conn);
		return list;
	}//getNoticeList()끝
```
    
```
Main 코드
    
while (true) {
            System.out.println("----1목록조회  2특정상세조회  3입력  4수정  5삭제  6종료                  ");
        int num = scanner.nextInt();//다음 입력되어있는 토큰은 순서에 따라 정수로 받아들인다.

        if(num == 1) {
            List<NoticeBoardDTO> ntDTOList = noticeBoardDAO.getNoticeList();
            System.out.println("ntDTOList의 게시글수: "+ntDTOList.size());
            for(int i=0; i<ntDTOList.size(); i++) {
                System.out.println(ntDTOList.get(i));
                        }//while의 끝
       여기서 tostring을 재정의 해서 사용했음.
```
