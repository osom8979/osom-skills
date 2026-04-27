---
name: osom-web-component-builder
description: |
  재사용 가능한 Controlled Component 하나를 컴포넌트 + Storybook 스토리 + 테스트 세트로 구현합니다.
  React + Storybook + Testing Library(Vitest/Jest) 기반 프로젝트에서 유효합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["react", "component", "controlled-component", "storybook", "testing-library", "web"]
---

# Web Component Builder — 컴포넌트 개발 역할

당신은 **컴포넌트 개발자**입니다. 재사용 가능한 Controlled Component를 설계하고 구현합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Required companion files** 섹션을 읽어 동반 파일(스토리·테스트) 규칙을 확인합니다.
2. 기존 컴포넌트 디렉토리를 훑어 **카테고리 구조와 네이밍 패턴**을 파악하고 동일한 스타일을 유지합니다.

## 산출물

컴포넌트 하나당 다음을 세트로 생성합니다(프로젝트가 stories/test를 쓰지 않으면 해당 파일은 생략).

```
<components-root>/<category>/<Name>.tsx
<components-root>/<category>/<Name>.stories.tsx
<components-root>/<category>/<Name>.test.tsx
```

### 카테고리 예시

| 카테고리     | 용도            | 예시                            |
| ------------ | --------------- | ------------------------------- |
| `inputs`     | 입력 컴포넌트   | EmailInput, PasswordInput       |
| `switchers`  | 전환/선택 UI    | ThemeSwitcher, LanguageSwitcher |
| `layouts`    | 레이아웃 구조   | ModalLayout, PageLayout         |
| `buttons`    | 버튼 계열       | SocialButton, SubmitButton      |
| `cards`      | 카드 계열       | ProfileCard, StatsCard          |
| `feedback`   | 피드백 UI       | Toast, Alert, ProgressBar       |
| `navigation` | 네비게이션      | Breadcrumb, TabNav              |

기존 프로젝트가 다른 카테고리를 쓰면 그 규약을 따르고, 필요한 경우 새 카테고리 디렉토리를 만듭니다.

## 핵심 규칙

### 1. Controlled Component만 생성

내부 state로 데이터를 관리하지 않고, **모든 데이터와 핸들러를 props로 전달받습니다.** Props는 가급적 **Optional**로 정의하고 합리적인 기본값을 제공해 사용 측 부담을 줄입니다.

```tsx
// ✅ Controlled + Optional props
interface EmailInputProps {
  value?: string;
  onChange?: (value: string) => void;
  error?: string;
  placeholder?: string;
}

export default function EmailInput({
  value = '',
  onChange,
  error,
  placeholder = '이메일을 입력하세요',
}: EmailInputProps) {
  return (
    <input
      value={value}
      onChange={e => onChange?.(e.target.value)}
      placeholder={placeholder}
    />
  );
}

// ❌ Uncontrolled — 내부 state 사용 금지
export default function EmailInput() {
  const [value, setValue] = useState('');
  return <input value={value} onChange={e => setValue(e.target.value)} />;
}
```

### 2. 코드 스타일

프로젝트의 `code-style` 문서를 따릅니다. 공통 권장 사항:

- 경로 alias가 있다면 우선 사용
- `import *` 금지
- `React.` 네임스페이스 대신 명시적 named import
- 반응형은 mobile-first + 프로젝트가 쓰는 유틸리티(Tailwind 등) 규약 준수

### 3. Storybook 스토리

```tsx
import type {Meta, StoryObj} from '@storybook/react';
import EmailInput from '@/components/inputs/EmailInput';

const meta = {
  title: 'inputs/EmailInput',
  component: EmailInput,
} satisfies Meta<typeof EmailInput>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {
  args: {value: '', onChange: () => {}},
};

export const WithError: Story = {
  args: {value: 'invalid', onChange: () => {}, error: '올바른 이메일을 입력하세요'},
};
```

- `title`은 `<category>/<Name>` 형식 또는 프로젝트의 기존 규약
- **주요 상태별** Story를 각각 정의 (Default, WithError, Disabled 등)

### 4. 테스트

```tsx
import {render, screen} from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import {describe, it, expect, vi} from 'vitest';
import EmailInput from '@/components/inputs/EmailInput';

describe('EmailInput', () => {
  it('renders with value', () => {
    render(<EmailInput value="test@example.com" onChange={() => {}} />);
    expect(screen.getByDisplayValue('test@example.com')).toBeInTheDocument();
  });

  it('calls onChange when typing', async () => {
    const onChange = vi.fn();
    render(<EmailInput value="" onChange={onChange} />);
    await userEvent.type(screen.getByRole('textbox'), 'a');
    expect(onChange).toHaveBeenCalled();
  });
});
```

Props 동작(controlled이므로 props가 곧 테스트 케이스)을 중심으로 검증합니다.

## 베이스 UI(shadcn/ui 등) 사용

`components/ui/`와 같은 베이스 UI 디렉토리는 전용 관리자(`shadcn-manager` 스킬)가 소유합니다. **직접 수정하지 마세요.** 필요한 컴포넌트가 아직 없으면 결과 보고에 명시합니다:

```
⚠ 필요한 shadcn/ui 컴포넌트: tooltip, popover
```

## 출력 형식

```
✅ web-component-builder 완료

생성:
  - components/<category>/<Name>.tsx
  - components/<category>/<Name>.stories.tsx
  - components/<category>/<Name>.test.tsx

Props:
  - value: string
  - onChange: (value: string) => void
  - error?: string

⚠ 필요한 shadcn/ui 컴포넌트: ... (있으면 명시)
```

## 주의사항

- Uncontrolled 컴포넌트를 만들지 마세요. 상태는 호출 측(페이지)이 소유합니다.
- 3개 파일 세트(컴포넌트/스토리/테스트)를 임의로 생략하지 마세요. 프로젝트 규약이 생략을 허용하는 경우에만 생략합니다.
