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

3. 페이징 모듈
   1) PagaingRequest.java 생성
```
package com.tiger.crm.repository.dto.page;

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
   2) 2
   3) 

