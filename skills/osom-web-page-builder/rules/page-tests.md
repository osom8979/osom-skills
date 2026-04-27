# 페이지 테스트

렌더링 성공, 주요 요소 존재, 핵심 플로우를 **최소로** 검증합니다. 페이지는 외부 의존성(라우터, 네트워크, 컨텍스트)이 많아 테스트 비용이 크므로 핵심 시나리오에 집중합니다.

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

세부 동작은 controlled component 단위 테스트에서 다루고, 페이지 테스트는 **조립 결과**를 검증합니다.
