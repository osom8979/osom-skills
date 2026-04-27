# Variants 추출 및 export

`cva`(class-variance-authority)로 정의된 variants가 있으면, 다른 컴포넌트에서 재사용할 수 있도록 **export 되어 있는지 확인**하고 없으면 추가합니다.

```tsx
export const buttonVariants = cva('...', {
  variants: {
    variant: {default: '...', destructive: '...'},
    size: {default: '...', sm: '...', lg: '...'},
  },
});
```

variants를 export하면 `<Link>` 같은 다른 컴포넌트에서도 `buttonVariants({variant: 'outline'})`처럼 동일한 스타일을 적용할 수 있습니다.
