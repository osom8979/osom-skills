# SQL 파일 네이밍 밴드

프로젝트의 `sql-naming` 문서에 정의된 번호 밴드를 따릅니다. 대표적인 예시:

```
postgres/<NN>-<domain>/<sub-domain>/
├── 10-tables-<name>.sql       ← 테이블 정의
├── 20-functions-<name>.sql    ← 함수
├── 30-triggers-<name>.sql     ← 트리거
├── 40-policies-<name>.sql     ← RLS 정책
├── 50-views-<name>.sql        ← 뷰
├── 70-datas.sql               ← 시드 데이터
├── 80-helpers-<name>.sql      ← 헬퍼 함수
└── 90-tests-<name>.sql        ← pgTAP 테스트
```

번호는 알파벳 순서 기반 실행을 보장합니다. 빈 번호 슬롯에 새 카테고리를 추가하려면 먼저 프로젝트의 `sql-naming` 문서를 갱신하세요.
