# import 정리

shadcn/ui가 설치 직후 생성하는 `import * as React`를 **실제 사용하는 항목으로 교체**합니다.

```tsx
// ❌ 설치 직후
import * as React from 'react';

// ✅ 정리 후
import {forwardRef, type ComponentPropsWithoutRef} from 'react';
```

파일 내에서 `React.`로 사용되는 항목을 모두 찾아 명시적 named import로 변환합니다. 와일드카드 import는 트리쉐이킹과 가독성을 모두 해칩니다.
