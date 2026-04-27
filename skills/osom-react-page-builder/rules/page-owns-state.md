# 페이지가 상태를 소유

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

상태 소유 위치는 **페이지(또는 그 하위 페이지 전용 컨테이너)** 입니다. controlled component는 받은 props만 렌더링합니다.
