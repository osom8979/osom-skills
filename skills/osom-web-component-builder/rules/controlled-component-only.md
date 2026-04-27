# Controlled Component만 생성

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

상태는 **호출 측(페이지)이 소유**합니다. 컴포넌트는 props가 곧 단일 진실원이며, 같은 props에 같은 출력을 보장합니다.
