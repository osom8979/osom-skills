# 페이지 스토리

페이지의 동반 stories 파일을 함께 생성합니다.

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

`title`은 `pages/<scope>/<Name>Page` 또는 프로젝트의 기존 규약을 따릅니다. 페이지 스토리는 라우터/네트워크 의존성이 있을 수 있으므로 데코레이터(MSW, MemoryRouter 등)로 감쌀 필요가 있는지 확인합니다.
