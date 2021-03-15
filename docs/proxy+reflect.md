# Proxy + Reflect

## Proxy 代理

**Proxy** 对象用于创建一个对象的代理，从而实现基本操作的`拦截`和自定义（如属性查找、赋值、枚举、函数调用等）

### 语法

```javascript
const proxy = new Proxy(target, handler);
```

**`target`**：需要代理的目标对象

**`handler`**：一个容纳一批特定属性的占位符对象

### handler 对象

**handler** 对象是一个容纳一批特定属性的占位符对象。它包含有`Proxy`的各个`捕获器（trap 陷阱函数）`

| handler 方法                     | Object                                                  |
| :------------------------------- | :------------------------------------------------------ |
| handler.get                      | 属性读取 getter                                         |
| handler.set                      | 属性设置 setter                                         |
| handler.has                      | in 操作符                                               |
| handler.ownKeys                  | Object.getOwnPropertyNames/Object.getOwnPropertySymbols |
| handler.deleteProperty           | delete 操作符                                           |
| handler.construct                | new 操作符                                              |
| handler.apply                    | 函数调用操作                                            |
| handler.getPrototypeOf           | Object.getPrototypeOf                                   |
| handler.setPrototypeOf           | Object.setPrototypeOf                                   |
| handler.isExtensible             | Object.isExtensible                                     |
| handler.preventExtensions        | Object.preventExtensions                                |
| handler.getOwnPropertyDescriptor | Object.getOwnPropertyDescriptor                         |
| handler.defineProperty           | Object.defineProperty                                   |

## Reflect 反射

**Reflect** 提供 拦截 JavaScript 各种操作的方法

### 静态方法

Reflect 不是一个构造函数，不能通过 new 操作符调用，它提供以下静态方法

| Reflect 静态方法                 | JavaScript 操作                                         |
| :------------------------------- | :------------------------------------------------------ |
| Reflect.get                      | 读取某属性的值                                          |
| Reflect.set                      | 设置某属性的值                                          |
| Reflect.defineProperty           | 定义新属性或修改某属性的值                              |
| Reflect.has                      | in 操作符                                               |
| Reflect.ownKeys                  | Object.getOwnPropertyNames/Object.getOwnPropertySymbols |
| Reflect.deleteProperty           | delete 操作                                             |
| Reflect.construct                | new Target                                              |
| Reflect.apply                    | Function.prototype.apply                                |
| Reflect.getPrototypeOf           | Object.getPrototypeOf                                   |
| Reflect.setPrototypeOf           | Object.setPrototypeOf                                   |
| Reflect.isExtensible             | Object.isExtensible                                     |
| Reflect.preventExtensions        | Object.preventExtensions                                |
| Reflect.getOwnPropertyDescriptor | Object.getOwnPropertyDescriptor                         |
