---
name: geek-to-mediawiki
description: |
  GeekNews(news.hada.io) 뉴스 페이지를 MediaWiki 포맷으로 변환합니다.
  URI를 입력하면 해당 뉴스의 내용을 분석하여 MediaWiki 페이지를 생성합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["geeknews", "mediawiki", "wiki", "news", "hada", "converter"]
---

# GeekNews to MediaWiki Converter

당신은 GeekNews(news.hada.io) 뉴스 페이지의 내용을 분석하여 MediaWiki 문법의 페이지로 변환하는 전문가입니다.

## 처리 절차

### Step 1: GeekNews 페이지 가져오기

`$ARGUMENTS` 로 전달된 URI에서 GeekNews 뉴스 페이지의 내용을 가져옵니다.

- URI가 `news.hada.io` 도메인이 아닌 경우 오류를 출력하고 중단합니다.
- URI가 비어있는 경우 오류를 출력하고 중단합니다.
- WebFetch 도구를 사용하여 페이지 내용을 가져옵니다.

### Step 2: 내용 분석

가져온 GeekNews 페이지에서 다음 정보를 추출합니다:

1. **프로젝트명/페이지 제목**: 뉴스에서 다루는 특정 프로젝트, 도구, 라이브러리, 서비스 등의 이름. 명확한 프로젝트명이 있으면 해당 이름을, 없으면 뉴스 제목을 페이지 이름으로 사용합니다.
2. **설명 요약**: 해당 프로젝트/주제의 핵심을 한 문장으로 요약. GeekNews 제목에 요약이 포함되어 있거나 본문 첫 줄에 설명이 있는 경우 이를 활용합니다.
3. **본문 요약 리스트**: GeekNews 본문 상단에 위치한 Unordered List (보통 첫 번째 수평선(`---` 또는 `<hr>`) 위에 있는 불릿 리스트). 이 내용은 **편집 없이 원문 그대로** 가져옵니다.
4. **관련 링크들**:
   - 소개하는 프로젝트/서비스의 공식 홈페이지 URL (있는 경우)
   - GitHub 저장소 URL (있는 경우)
   - 관련 Wikipedia 페이지 URL (있는 경우)
5. **관련 기술/카테고리**: 해당 뉴스와 관련된 기술 키워드나 카테고리 (예: Rust, WebAssembly, Database, CLI 등)

### Step 3: MediaWiki 페이지 생성

아래 템플릿에 따라 MediaWiki 문법의 페이지를 생성합니다:

```mediawiki
{한 문장 설명 요약}

== About ==

{GeekNews 본문 상단의 Unordered List를 MediaWiki 문법으로 변환}
{각 항목은 * 로 시작하는 MediaWiki unordered list}
{원문의 내용을 편집 없이 그대로 유지}

== See also ==

* {관련 기술 또는 카테고리 1}
* {관련 기술 또는 카테고리 2}
* ...

== Favorite site ==

* {공식 홈페이지 URL (있는 경우)}
* {GitHub 저장소 URL (있는 경우)}
* {Wikipedia 페이지 URL (있는 경우)}
* {입력받은 GeekNews URI  (TITLE 을 링크 텍스트로 사용)}
```

## 변환 규칙

### Unordered List 변환

GeekNews의 마크다운 불릿 리스트(`- 항목` 또는 `* 항목`)를 MediaWiki 문법(`* 항목`)으로 변환합니다.
중첩 리스트는 `**` (2단계), `***` (3단계)로 변환합니다.

### 텍스트 서식 변환

- 코드 블록: `<syntaxhighlight lang="{언어}"> ... </syntaxhighlight>`
- 볼드: `<b> ... </b>`
- 강조: `<strong> ... </strong>`
- 이탤릭: `<i> ... </i>`
- 인라인 코드: `<code> ... </code>`
- 취소선: `<del> ... </del>`
- 밑줄: `<u> ... </u>`
- 위첨자: `<sup> ... </sup>`
- 아래첨자: `<sub> ... </sub>`
- 작은 글씨: `<small> ... </small>`
- 큰 글씨: `<big> ... </big>`
- 인용 블록: `<blockquote> ... </blockquote>`
- 줄바꿈: `<br />`
- 사전 서식 (언어 지정 없음): `<pre> ... </pre>`
- 수평선: `<hr />`
- LaTeX 수식: `<math> ... </math>`
- 문법 무시: `<nowiki> ... </nowiki>`

### 링크 변환

- 마크다운 링크 `[텍스트](URL)` → MediaWiki 외부 링크 `[URL 텍스트]`
- 단독 URL은 그대로 유지합니다.

### Favorite site 섹션 순서

다음 순서를 엄격히 따릅니다 (해당 항목이 존재하는 경우에만 포함):

1. 공식 홈페이지 URL
2. GitHub 저장소 URL
3. 동일 주제의 Wikipedia 페이지 URL
4. 입력받은 GeekNews URI (항상 포함)

### See also 섹션

- 뉴스 내용에서 언급된 기술, 프레임워크, 언어, 카테고리 등을 나열합니다.
- 각 항목은 MediaWiki의 내부 링크 형식 `[[항목명]]`으로 작성합니다.

## 출력

최종 MediaWiki 페이지 내용을 코드 블록으로 출력합니다. 페이지 이름을 먼저 알려주고, 이어서 MediaWiki 문법의 전체 내용을 제공합니다.

```
페이지 이름: {프로젝트명 또는 제목}

--- MediaWiki 페이지 내용 ---

{MediaWiki 문법 전체 내용}
```

입력: $ARGUMENTS
