# @vue/reactivity

## 介绍

Vue3 基于 [ES2015 Proxy][mdn-proxy] 实现数据响应式，`@vue/reactivity` 是 Vue3 实现数据响应式的核心模块

事实上你完全可以独立使用`@vue/reactivity`

```javascript
// test.js
const { effect, reactive } = require('@vue/reactivity');

// 定义响应式数据
const obj = reactive({ count: 0 });

// 定义副作用函数
effect(() => {
  console.log(obj.count);
});

// 修改数据
setInterval(() => obj.count++, 1000);
```

## reactive()

reactive 用于定义响应式数据

```typescript
const obj = reactive({ count: 0 });
```

源码中 reactive 通过`new Proxy(target, mutableHandlers/mutableCollectionHandlers)`实现

- [mutableCollectionHandlers][sc-mutablecollectionhandlers] 实现 Map/Set/WeakMap/WeakSet 代理
- [mutableHandlers][sc-mutablehandlers] 代理其他基础类型数据

```typescript
function createReactiveObject(
  target: Target,
  isReadonly: boolean,
  baseHandlers: ProxyHandler<any>,
  collectionHandlers: ProxyHandler<any>,
) {
  // ...

  // 使用 proxy 来创建响应式对象
  const proxy = new Proxy(
    target,
    targetType === TargetType.COLLECTION ? collectionHandlers : baseHandlers,
  );

  return proxy;
}
```

### [mutableHandlers][sc-mutablehandlers]

```typescript
export const mutableHandlers: ProxyHandler<object> = {
  get, // createGetter -> getter
  set, // createSetter -> setter
  deleteProperty, // delete
  has,
  ownKeys,
};
```

在 `getter` 中，使用 `track(target, "get", key)` 收集依赖 [source code][sc-mutablehandlers-get]

```typescript
if (!isReadonly) {
  track(target, TrackOpTypes.GET, key);
}
```

在 `setter` 中，使用 `trigger(target, "set", key, value, oldValue)` 派发更新 [source code][sc-mutablehandlers-set]

```typescript
// don't trigger if target is something up in the prototype chain of original
if (target === toRaw(receiver)) {
  if (!hadKey) {
    trigger(target, TriggerOpTypes.ADD, key, value); // 新增
  } else if (hasChanged(value, oldValue)) {
    trigger(target, TriggerOpTypes.SET, key, value, oldValue); // 赋值
  }
}
```

在 `deleteProperty` 中，使用 `trigger(target, "delete", key)` 派发更新 [source code][sc-mutablehandlers-set]

```typescript
function deleteProperty(target: object, key: string | symbol): boolean {
  const hadKey = hasOwn(target, key);
  const oldValue = (target as any)[key];
  const result = Reflect.deleteProperty(target, key);
  if (result && hadKey) {
    trigger(target, TriggerOpTypes.DELETE, key, undefined, oldValue); // 派发更新
  }
  return result;
}
```

参考链接:

- [MDN - Proxy][mdn-proxy]
- [Vue.js - 响应性 API](https://v3.cn.vuejs.org/api/reactivity-api.html)
- [深入理解 Vue3 Reactivity API](https://zhuanlan.zhihu.com/p/146097763)

<!-- links -->

[mdn-proxy]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

<!-- source code -->

[sc-create-reactive-object]: https://github.com/vuejs/vue-next/blob/a26cd9ca7ff8e8f987ac86925a7c861536582d3e/packages/reactivity/src/reactive.ts#L197-L202
[sc-mutablehandlers]: https://github.com/vuejs/vue-next/blob/a26cd9ca7ff8e8f987ac86925a7c861536582d3e/packages/reactivity/src/baseHandlers.ts#L190-L196
[sc-mutablecollectionhandlers]: https://github.com/vuejs/vue-next/blob/a26cd9ca7ff8e8f987ac86925a7c861536582d3e/packages/reactivity/src/collectionHandlers.ts#L331-L333
[sc-mutablehandlers-get]: https://github.com/vuejs/vue-next/blob/a26cd9ca7ff8e8f987ac86925a7c861536582d3e/packages/reactivity/src/baseHandlers.ts#L104-L106
[sc-mutablehandlers-set]: https://github.com/vuejs/vue-next/blob/a26cd9ca7ff8e8f987ac86925a7c861536582d3e/packages/reactivity/src/baseHandlers.ts#L155-L162
[sc-mutablehandlers-delete]: https://github.com/vuejs/vue-next/blob/a26cd9ca7ff8e8f987ac86925a7c861536582d3e/packages/reactivity/src/baseHandlers.ts#L171-L173
