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

