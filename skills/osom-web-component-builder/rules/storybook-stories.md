# Storybook 스토리

생성하는 컴포넌트 옆에 동반 stories 파일을 둡니다.

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

- `title`은 `<category>/<Name>` 형식 또는 프로젝트의 기존 규약을 따릅니다.
- **주요 상태별** Story를 각각 정의 (Default, WithError, Disabled 등).
