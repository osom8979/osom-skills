# 스키마 검증 정규 묶음

테이블이 만들어졌는지보다 **컬럼 타입·NULL·DEFAULT·제약·인덱스**까지 단단히 잠그는 게 회귀 방지에 효과적:

```sql
-- 컬럼 존재 + 타입
SELECT has_column('public', 'orgs', 'name', 'orgs.name exists');
SELECT col_type_is('public', 'orgs', 'name', 'text', 'orgs.name is text');
SELECT col_not_null('public', 'orgs', 'name', 'orgs.name is NOT NULL');
SELECT col_has_default('public', 'orgs', 'created_at', 'orgs.created_at has default');
SELECT col_default_is('public', 'orgs', 'created_at', 'now()',
    'default is now()');

-- PK / FK
SELECT col_is_pk('public', 'orgs', 'id', 'orgs.id is PK');
SELECT fk_ok('public', 'org_members', ARRAY['org_id'],
              'public', 'orgs',         ARRAY['id'],
    'org_members.org_id references orgs.id');

-- UNIQUE / CHECK
SELECT col_is_unique('public', 'orgs', 'slug', 'orgs.slug is UNIQUE');
SELECT has_check('public', 'orgs', 'orgs has CHECK constraint');

-- 인덱스
SELECT has_index('public', 'orgs', 'orgs_slug_idx', ARRAY['slug'],
    'orgs has index on slug');
SELECT index_is_unique('public', 'orgs_slug_idx', 'index is UNIQUE');

-- ENUM
SELECT enum_has_labels('public', 'role',
    ARRAY['owner', 'maintainer', 'developer', 'viewer'],
    'role enum has expected labels');
```

전수 검사용 `*_are`는 **드리프트 검출에 유용**하지만 새 컬럼 추가 시마다 깨지므로, **핵심 도메인 객체에만 한정**:

```sql
SELECT columns_are('public', 'orgs', ARRAY['id','slug','name','created_at','updated_at'],
    'orgs columns frozen');
```
