# Archive_Util
개발 중 필요한 유틸

<br/><br/>

1. 정규표현식 관련
  - HTML 삭제

```
// test : "<특수문자><u>20240925</u><b>hello</b><script>z</script><특수문자>[특수]{특수}<h>특</h>"
// removeHtmlTags : 모든 HTML 태그를 제거하되 일반 특수문자는 유지
public static String removeHtmlTags(String source) {
  if (source == null) return null;
  String returnStr = source.replaceAll("<(/)?([a-zA-Z]*)(\\\\s[a-zA-Z]*=[^>]*)?(\\\\s)*(/)?>", " ");
  returnStr = returnStr.replaceAll("\\s+", " ").trim();
  returnStr = nl2str(returnStr, "<br>");
  return returnStr;
}
```

<br/><br/>

2. 파일 유틸
- Apache Tika 를 이용한 파일 타입 추출(참고: https://glenn-dev.tistory.com/43)
```
파일의 Mime Type 을 추출한다.
public static String getMimeTypeByTika(File file) throws IOException {
  Tika tika = new Tika();
  return tika.detect(file);
}
```

<br/><br/>

3. 페이징 모듈
  - 0. PageUtils 생성
  ```
  import java.util.ArrayList;
  import java.util.List;
  
  public class PageUtils2 {
  
      /**
       * 페이지 계산 로직을 처리하는 메서드
       * @param totalRecords 전체 레코드 수
       * @param recordsPerPage 페이지당 레코드 수
       * @param currentPage 현재 페이지 번호
       * @return 페이지 번호 리스트
       */
      public static List<Integer> makePages(int totalRecords, int recordsPerPage, int currentPage) {
          List<Integer> pages = new ArrayList<>();
  
          if (recordsPerPage <= 0) recordsPerPage = 10; // 기본값 설정
          if (currentPage <= 0) currentPage = 1; // 기본값 설정
  
          int totalPages = (int) Math.ceil((double) totalRecords / recordsPerPage);
  
          // 예시: 5페이지씩 묶어서 보여주기
          int startPage = ((currentPage - 1) / 5) * 5 + 1;
          int endPage = Math.min(startPage + 4, totalPages);
  
          for (int i = startPage; i <= endPage; i++) {
              pages.add(i);
          }
  
          return pages;
      }
  }
  ```


  - 1. PagingRequest.java 생성
  ```
  import lombok.Getter;
  import lombok.Setter;
  
  @Getter
  @Setter
  public class PagingRequest {
      private int page;                     // 현재 페이지 번호
      private int size;                     // 한 페이지에 보여줄 데이터 개수
      private String searchKeyword;         // 검색어
      private String searchType;            // 검색 타입
      private String startDt;
      private String endDt;
  
      public PagingRequest() {
          this.page = 1;  // 기본값 설정
          this.size = 5; // 기본값 설정
      }
  
      public int getOffset() {
          return (page - 1) * size;
      }
  }
  ```
 - 2. PagingResponse.java 생성
   ```
    import com.tiger.crm.common.util.PageUtils2;
    import lombok.Getter;
    import java.util.List;
    
    @Getter
    public class PagingResponse<T> {
        private final List<T> dataList;     // 페이지 데이터 리스트
        private final int totalElements;   // 전체 데이터 개수
        private final int totalPages;      // 전체 페이지 수
        private final int currentPage;     // 현재 페이지 번호
        private final int pageSize;        // 한 페이지에 보여줄 데이터 개수
        private final List<Integer> pages; // 페이지 번호 리스트

        public PagingResponse(List<T> dataList, int totalElements, PagingRequest pagingRequest) {
          this.dataList = dataList;
          this.totalElements = totalElements;
          this.pageSize = pagingRequest.getSize();
          this.currentPage = pagingRequest.getPage();
          this.totalPages = (int) Math.ceil((double) totalElements / pageSize);
          this.pages = PageUtils2.makePages(totalElements, pageSize, currentPage);
        }
    }
   ```
- 3. Controller > Service > ServiceImpl > Mapper > SQL
   ```
      1. Controller
         @ModelAttribute PagingRequest pagingRequest
         // 입력 값 검증 및 기본값 설정
         PagingResponse<Map<String, Object>> pageResponse = ticketService.getTicketList2(pagingRequest);
		     model.addAttribute("ticketList", pageResponse);
      2. Service
         public interface TicketService {
             // 페이징 객체를 사용한 리스트
             PagingResponse<Map<String, Object>> getTicketList2(PagingRequest pagingRequest);
         }
       3. ServiceImpl
          @Service
          public class TicketServiceImpl implements TicketService {
              @Autowired
              private TicketMapper ticketMapper;

              @Override
              public PagingResponse<Map<String, Object>> getTicketList2(PagingRequest pagingRequest) {
                List<Map<String, Object>> ticketList = ticketMapper.getTicketList2(pagingRequest);
                int totalRecords = ticketMapper.getTicketListCount2(pagingRequest);
                // 페이징 응답 객체 생성
                return new PagingResponse<>(ticketList, totalRecords, pagingRequest);
              }

          }
   ```

      4. Mapper
   ```
         @Mapper
         public interface TicketMapper {
             // 결과를 List<Map<String, Object>>로 받는 경우, @Param 어노테이션 사용
             List<Map<String, Object>> getTicketList2(PagingRequest pagingRequest);
      
             int getTicketListCount2(PagingRequest pagingRequest);
         }
   ```
