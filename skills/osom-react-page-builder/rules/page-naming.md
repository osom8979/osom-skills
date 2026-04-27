# 페이지 네이밍

`export default function <Name>Page()` 형태로 작성합니다. 간단한 `<Name>`이나 `index` 네이밍은 IDE 탭/스택 트레이스 가독성을 해칩니다.

```tsx
// ✅
export default function SignInPage() { ... }

// ❌
export default function SignIn() { ... }
export default function index() { ... }
```

`Page` 접미사는 디렉토리 이름과 충돌하지 않으면서 컴포넌트인지 라우트 페이지인지 한눈에 구분되게 해줍니다.
