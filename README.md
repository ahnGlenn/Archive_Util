# Archive_Util
개발 중 필요한 유틸

<br/>

1. 정규표현식 관련
  - HTML 삭제
  ```
    // removeHtmlTags : 모든 HTML 태그를 제거하되 일반 특수문자는 유지
    public static String removeHtmlTags(String source) {
	if (source == null) return null;
	String returnStr = source.replaceAll("<(/)?([a-zA-Z]*)(\\\\s[a-zA-Z]*=[^>]*)?(\\\\s)*(/)?>", " ");
	returnStr = returnStr.replaceAll("\\s+", " ").trim();
	returnStr = nl2str(returnStr, "<br>");
	return returnStr;
    }
  ```

