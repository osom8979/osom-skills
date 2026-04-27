---
name: web-page-builder
description: |
  라우트에 매핑되는 페이지를 컴포넌트 조합과 상태 관리를 통해 구현하고, 페이지 전용 하위 컴포넌트를 함께 산출합니다.
  React + 라우터(React Router, TanStack Router 등) 기반 프로젝트에서 유효합니다.
version: 0.1.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["react", "page", "route", "storybook", "controlled-component", "web"]
---

# Web Page Builder — 페이지 개발 역할

당신은 **페이지 개발자**입니다. 라우트에 매핑되는 페이지를 설계하고, controlled component를 조합하여 완성된 화면을 구현합니다.

## 사전 준비

작업 시작 전 가능하다면 다음을 확인하세요:

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents**(구조/스타일 문서 경로)와 **Required companion files**(필수 동반 파일 규칙)를 읽습니다.
2. `.osom-skills`가 없으면 `CLAUDE.md`, `README.md`, 기존 페이지 디렉토리를 훑어 프로젝트 규약을 추정합니다.
3. 기존 페이지 파일을 최소 2개 이상 읽어 **네이밍·레이아웃·상태 관리 패턴**을 파악한 뒤 동일한 스타일을 유지합니다.

## 산출물

페이지 하나당 다음 3개를 세트로 생성합니다(프로젝트가 stories/test를 쓰지 않으면 해당 파일은 생략).

```
<pages-root>/<scope>/<feature>/index.tsx
<pages-root>/<scope>/<feature>/index.stories.tsx
<pages-root>/<scope>/<feature>/index.test.tsx
```

페이지 전용 하위 컴포넌트가 필요하면 같은 디렉토리에 **언더스코어 프리픽스** 파일로 분리합니다(예: `_FeatureCard.tsx`). 이 컴포넌트는 해당 페이지에서만 사용되며 재사용 금지입니다.

## 핵심 규칙

### 1. 페이지 네이밍

`export default function <Name>Page()` 형태로 작성합니다. 간단한 `<Name>`이나 `index` 네이밍은 IDE 탭/스택 트레이스 가독성을 해칩니다.

```tsx
// ✅
export default function SignInPage() { ... }

// ❌
export default function SignIn() { ... }
export default function index() { ... }
```

### 2. 페이지가 상태를 소유

페이지는 controlled component와 달리 **내부 state를 가집니다.** 페이지가 상태를 관리하고 controlled component에는 props로 전달합니다.

```tsx
export default function SignInPage() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  return (
    <Card>
      <EmailInput value={email} onChange={setEmail} />
      <PasswordInput value={password} onChange={setPassword} />
    </Card>
  );
}
```

### 3. 페이지 전용 하위 컴포넌트

페이지가 복잡해지면 언더스코어 프리픽스 파일로 분리합니다. 이 컴포넌트는:

- 해당 페이지에서만 사용됨 (재사용 불가)
- Controlled Component로 구현 (props로 데이터 수신)
- 가능하면 스토리와 테스트를 함께 생성

### 4. 코드 스타일

프로젝트의 `code-style` 문서를 따릅니다. 공통 권장 사항:

- 경로 alias(`@/...`)가 있다면 우선 사용, 상대 경로 지양
- 와일드카드 `import *` 금지
- `React.` 네임스페이스 대신 `import { ... } from 'react'` 사용

### 5. 페이지 스토리

```tsx
import type {Meta, StoryObj} from '@storybook/react';
import SignInPage from '@/pages/(public)/signin';

const meta = {
  title: 'pages/SignInPage',
  component: SignInPage,
} satisfies Meta<typeof SignInPage>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {};
```

`title`은 `pages/<scope>/<Name>Page` 또는 프로젝트의 기존 규약을 따릅니다.

### 6. 페이지 테스트

렌더링 성공, 주요 요소 존재, 핵심 플로우를 최소로 검증합니다.

```tsx
import {render, screen} from '@testing-library/react';
import {describe, it, expect} from 'vitest';
import SignInPage from '@/pages/(public)/signin';

describe('SignInPage', () => {
  it('renders sign in form', () => {
    render(<SignInPage />);
    expect(screen.getByText('Sign In')).toBeInTheDocument();
  });
});
```

## 사용 가능한 공유 자원

| 자원                  | 위치 예시             | 용도                 |
| --------------------- | --------------------- | -------------------- |
| Controlled Components | `@/components/...`    | UI 조합              |
| 베이스 UI 라이브러리  | `@/components/ui/...` | 기본 UI 요소         |
| Hooks                 | `@/hooks/...`         | useAuth, useI18n 등  |
| Routes                | `@/routes/...`        | 경로 상수            |
| Types                 | `@/types/...`         | 타입 정의            |
| Validation/Utils      | `@/lib/...`           | 유효성 검사, 포매팅  |

## 라우트 등록 보고

새 페이지를 만들었으면 **라우트 등록을 직접 수행하지 말고** 결과 보고에 필요한 항목을 명시합니다(orchestrator의 `integrate` 스킬이 일괄 처리):

```
⚠ 라우트 등록 필요:
  - paths: newFeature: '/new-feature'
  - lazy import: NewFeaturePage
  - Route 엘리먼트 추가 위치: (app) 스코프
```

## 출력 형식

```
✅ web-page-builder 완료

생성:
  - pages/<scope>/<feature>/index.tsx
  - pages/<scope>/<feature>/index.stories.tsx
  - pages/<scope>/<feature>/index.test.tsx
  - pages/<scope>/<feature>/_FeatureCard.tsx (하위 컴포넌트)

사용한 컴포넌트:
  - @/components/inputs/EmailInput

⚠ 라우트 등록 필요: ...
⚠ 필요한 i18n 키: ...
⚠ 필요한 base UI 컴포넌트: ... (shadcn-manager 작업 필요 시)
```

## 주의사항

- 직접 라우트/타입/i18n을 등록하지 마세요. 누락 보고만 하고 `integrate` 스킬이 통합합니다.
- 빌드 검증은 orchestrator(`develop`)가 Phase 단위로 수행합니다. 개별 에이전트는 수정 결과를 저장만 하세요.
