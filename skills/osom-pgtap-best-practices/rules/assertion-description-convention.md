# assertion description 컨벤션

David Wheeler: "test description은 검색 가능하게 하라." 권장 prefix:

```
[<scope>/<action>] <expected behavior>
```

예:

```
'[orgs/SELECT] member can view org'
'[orgs/INSERT] outsider blocked'
'[org_role/owner] returns owner role'
'[trigger/sole-owner] cannot remove last owner'
'[create_org/auth] anon cannot call'
```

`grep '\[orgs/'`로 카테고리 필터링이 가능해지고, 실패 메시지에서 위치 추적이 즉각.

## 테스트 description 언어

- **assertion message는 영어** (TAP 출력 도구·로그 호환).
- SQL 주석(`--`)은 프로젝트 정책에 따라 한국어 가능.
