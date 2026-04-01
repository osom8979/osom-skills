# geek-to-mediawiki

GeekNews(news.hada.io) 뉴스 페이지를 MediaWiki 포맷으로 변환하는 스킬입니다.

## 설치

```bash
claude plugin install geek-to-mediawiki@osom-plugins
```

## 스킬 목록

| 스킬 | 설명 |
|------|------|
| `/geek-to-mediawiki` | GeekNews 뉴스를 MediaWiki 페이지로 변환 |

## 사용 예시

```bash
/geek-to-mediawiki https://news.hada.io/topic?id=12345
```

## 변환 결과 구조

생성되는 MediaWiki 페이지는 다음과 같은 구조를 가집니다:

1. **첫 줄**: 프로젝트/주제에 대한 한 문장 설명
2. **About 섹션**: GeekNews 본문의 요약 리스트 (원문 그대로)
3. **See also 섹션**: 관련 기술 및 카테고리
4. **Favorite site 섹션**: 공식 홈페이지, GitHub, Wikipedia, GeekNews 링크

## 라이선스

MIT
