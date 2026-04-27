# Storybook 스토리 생성

설치한 컴포넌트의 스토리 파일을 다음 위치에 생성합니다.

```
<components-root>/ui/<component>.stories.tsx
```

```tsx
import type {Meta, StoryObj} from '@storybook/react';
import {Button} from '@/components/ui/button';

const meta = {
  title: 'ui/Button',
  component: Button,
} satisfies Meta<typeof Button>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {args: {children: 'Button'}};
export const Secondary: Story = {args: {variant: 'secondary', children: 'Secondary'}};
export const Destructive: Story = {args: {variant: 'destructive', children: 'Destructive'}};
```

- `title`은 `ui/<ComponentName>` 형식.
- **모든 variant**(size, variant 등 주요 props 조합)에 대해 각각 Story 정의.
