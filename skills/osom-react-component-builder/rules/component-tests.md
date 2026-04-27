# 컴포넌트 테스트

생성하는 컴포넌트 옆에 동반 test 파일을 둡니다.

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

Props 동작(controlled이므로 props가 곧 테스트 케이스)을 중심으로 검증합니다. 내부 구현이 아니라 **사용자 관점의 동작**을 테스트합니다.
