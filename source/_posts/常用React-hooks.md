---
title: 常用React hooks
date: 2021-04-05 21:32:11
tags:
  - React
  - Web开发
---

React hooks 已经很普及了。记录和整理一些比较通用的 hook。

## useMount

返回组件挂载的状态。可以在一些异步处理之后，判断组件是否已经被卸载。

```typescript
function useMount() {
  const mount = useRef(false);
  useEffect(() => {
    mount.current = true;
    return () => {
      mount.current = false;
    };
  }, []);

  return () => mount.current;
}
```

## usePreProps

返回上一次的 props，也可以用来判断某个 props 是否改变。

```typescript
function usePreProps(props) {
  const ref = useRef(props);
  useEffect(() => {
    ref.current = props;
  }, [props]);

  // return ref.current === props; // 判断属性是否改变
  return ref.current; // 返回上一次的属性
}
```

### useOnScreen

返回组件是否曝光。

```typescript
const useOnScreen = (ref: React.RefObject<any>, rootMargin = "0px") => {
  if (!ref || !ref.current) return;

  const [intersecting, setIntersecting] = useState(false); // 是否已经曝光

  useEffect(() => {
    const ovserver = new IntersectionObserver(
      ([entry]) => {
        setIntersecting(entry.isIntersecting);
      },
      { rootMargin }
    );

    ovserver.observe(ref.current);

    return () => {
      observer.unobserve(ref.current);
    };
  }, []);

  return [intersecting];
};
```
