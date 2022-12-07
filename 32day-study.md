	## 1. Map,Set를 이용한 DB연결 DML (DAO)
 ``` 
  //1-1. 목록조회 (set)
	public Set<NoticeBoardDTO> getNoticeList() {
		System.out.println("getNoticeList()진입");
		PreparedStatement stmt = null;
		Connection conn = null;
		ResultSet rs = null;
		
		//인터페이스타입 참조변수명 =new 구현클래스명();
		 Set<NoticeBoardDTO> list = new HashSet(); 
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
		} finally { // exception이 발생해도 안해도 무조건 거쳐간다
			//5.반납
			Jdbcutil.close(rs);
			Jdbcutil.close(stmt);
			Jdbcutil.close(conn);
		}
		return list;
	}//getNoticeList()끝
	
	
	
	//1-2. 목록조회 (map)
		public Map<Integer,NoticeBoardDTO> getNoticeList2() {
			System.out.println("getNoticeList()진입");
			PreparedStatement stmt = null;
			Connection conn = null;
			ResultSet rs = null;
			
			//인터페이스타입 참조변수명 =new 구현클래스명();
			Map<Integer,NoticeBoardDTO> list = new HashMap<Integer,NoticeBoardDTO>(); 
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
				//방법2. 파라미터가 있는 생성자를 이용하여 객체생성하기.
				NoticeBoardDTO nbdto1= new NoticeBoardDTO(rs.getInt("nbno"),rs.getString("title"),rs.getString("contant"),rs.getDate("cre_date"),rs.getString("writer"),rs.getInt("rcnt"),rs.getInt("empno"));
				
				
			//방법1. 기본생성자를 이용하여 객체를 생성하는 경우.
				/*NoticeBoardDTO nbdto= new NoticeBoardDTO();
				nbdto.setNbno(nbno);
				nbdto.setTitle(title);
				nbdto.setContent(contant);
				nbdto.setCre_date(cre_date);
				nbdto.setWriter(writer);
				nbdto.setRcnt(rcnt);
				nbdto.setEmpno(empno);*/
				
			//공지사항게시판의 객체를 list에 추가한다.
				list.put(nbno,nbdto1);
				
				/*System.out.printf("%5d\t%s\t%s\t%s",nbno,title,cre_date,writer);
				System.out.println();//줄바꿈*/
				
				}//while의 끝
			} catch (SQLException e) {
				e.printStackTrace();
			} finally { // exception이 발생해도 안해도 무조건 거쳐간다
				//5.반납
				Jdbcutil.close(rs);
				Jdbcutil.close(stmt);
				Jdbcutil.close(conn);
			}
			return list;
		}//getNoticeList()끝
 ``` 
  	## 2. Map,Set를 이용한 DB연결 DML (main)
  ```
  public class Main {

	public static void main(String[] args) {
		
		Scanner scanner= new Scanner(System.in);
		NoticeBoardDAO noticeBoardDAO = new NoticeBoardDAO();
		
		while (true) {
					System.out.println("----0목록조회(map) 1목록조회(set)  2특정상세조회  3입력  4수정  5삭제  6종료                  ");
				int num = scanner.nextInt();//다음 입력되어있는 토큰은 순서에 따라 정수로 받아들인다.
					if (num ==0) {
						Map<Integer,NoticeBoardDTO> list = noticeBoardDAO.getNoticeList2();
						Set<Map.Entry<Integer,NoticeBoardDTO>> i1 = list.entrySet();
						Iterator i2 = i1.iterator();
						System.out.println("아래는 entry set이용");
						while(i2.hasNext()) {
							Object i3 = i2.next();
							System.out.println(i3);
						}
						
						
						System.out.println("아래는 keyset이용");
						 Set<Integer> i4 = list.keySet();
						Iterator<Integer> i5 = i4.iterator();
						while(i5.hasNext()) {
							int i6 = i5.next();
							NoticeBoardDTO i7 = list.get(i6);
							System.out.println(i7);
						} 
					}
					
					
					
					else if(num == 1) {
					Set<NoticeBoardDTO> ntDTOList = noticeBoardDAO.getNoticeList();
					System.out.println("ntDTOList의 게시글수: "+ntDTOList.size());
					
					Iterator<NoticeBoardDTO> iterset = ntDTOList.iterator();
					while(iterset.hasNext()) {
						NoticeBoardDTO dto = iterset.next();
						System.out.println(dto);
					} 
					
					/* for(NoticeBoardDTO i : ntDTOList) {
						System.out.println(i); 
					}
					
					for(int i=0; i<ntDTOList.size(); i++) {
						System.out.println(ntDTOList.get(i));
								}while의 끝*/
  ```
