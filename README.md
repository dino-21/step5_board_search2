
![32](https://github.com/dino-21/step5_board_search/assets/80396471/5baac528-350e-4f8f-aaca-0ceb7ade26aa)





1. <select>태그를 이용해서 검색

2. 다중 항목 검색 
제목(title)이나 내용(content) 중에 test라는 문자열이 있는 게시물들을 검색


3.  MyBatis의 동적 SQL



4. 검색 조건 처리를 위한 Criteria의 변화
 검색 조건 처리가 들어가면 Criteria 역시 수정
private String type;  //검색조건, T(제목), C(내용), W(작성자)
private String keyword;  //검색조건
  //TCW(문자열)        T C W   검색조건
  public String[] getTypeArr() {
	 return type==null? new String[] {} : type.split("");
 }


5. BoardMapper.java 인터페이스 수정
public List<BoardVO> searchTest(Map<String, Map<String, String>> map);


6. SQL문 만들기
<!--  검색조건1  key, val 찍히는 내용 검색--> 
<select id="searchTest" resultType="org.zerock.domain.BoardVO">
	 <![CDATA[
		select * from tbl_board
		where
	 ]]>
		 
    <trim prefix="(" suffix=") and"> 		
	<foreach item="val"  index="key" collection="map" separator="or">
    
             <if test="key == 'T'.toString()">
		      title like '%' || #{val} || '%'
             </if>
	         <if test="key == 'C'.toString()">
		      content like '%' || #{val} || '%'
	         </if>
	         <if test="key == 'W'.toString()">
		      writer like '%' || #{val} || '%'
		 </if>
	</foreach>
    </trim>
		
	<![CDATA[
	 rownum < 10
	 ]]>
</select>



7. BoardMapper.xml  코드 수정
 criterial를 함수로 묶어서 호출
<include refid="criterial"></include>


8. BoardMapperTests.java  코드 추가 
@Test
public void testgetListWithPaging() {
	Criteria cri = new Criteria();
		
	cri.setKeyword("user00");
	cri.setType("TCW");
		
	mapper.getListWithPaging(cri)
	.forEach(list-> log.info(list));
		
}


9. 화면에서 검색 조건 처리
<form>태그와 <option>태그만들기


10. 검색 버튼의 이벤트 처리 js처리

검색은 1페이지를 하도록 수정하고,
화면에 검색 조건과 키워드가 보이게 처리



11. lists.jsp에서 검색 조건과 키워드 보여주는 부분 수정

<option value=""<c:out value="${pageMaker.cri.type == null?'selected':''}"/>>--</option>
<option value="T"<c:out value="${pageMaker.cri.type eq 'T'?'selected':''}"/>>제목</option>
<option value="C"<c:out value="${pageMaker.cri.type eq 'C'?'selected':''}"/>>내용</option>


12. 페이지 번호를 클릭해서 이동할 때에도 검색 조건과 키워드가 같이 전달되어야 하므로 페이지 이동에 사용한
<form>태그를 수정해야 한다.
<input type='hidden' name='type' value='${pageMaker.cri.type}'>
<input type='hidden' name='keyword' value='${pageMaker.cri.keyword}'>


13. 조회 페이지에서 검색 처리 get.jsp 수정
<input type='hidden' name='type' value='${cri.type}'>
<input type='hidden' name='keyword' value='${cri.keyword}'>

14. Modify.jsp
<input type='hidden' name='type' value='${cri.type}'>
<input type='hidden' name='keyword' value='${cri.keyword}'>


15. BoardController.java 코드 추가

수정/삭제 처리는 BoardController에서 redirect 방식으로 동작하므로 type과 keyword 조건을 같이 리다이렉트시에 포함시켜야 한다.

rttr.addAttribute("type", cri.getType());
rttr.addAttribute("keyword", cri.getKeyword());





16. 삭제 페이지에서 검색처리
BoardController.java 코드 추가
rttr.addAttribute("type", cri.getType());
rttr.addAttribute("keyword", cri.getKeyword());




