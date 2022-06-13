## 题目
**获取函数返回类型** 

不使用 `ReturnType` 实现 TypeScript 的 `ReturnType<T>` 泛型。

例如：

```ts
const fn = (v: boolean) => {
  if (v)
    return 1
  else
    return 2
}

type a = MyReturnType<typeof fn> // 应推导出 "1 | 2"
```
## 解答
主要还是利用`infer`关键字**类型推导**的作用。它会在类型未推导时进行占位，等到真正推导成功后，它能准确地返回正确的类型。在[TypeScript类型体操中的关键字详解（三）](https://juejin.cn/post/7106038466139389959)中我们提到`extends`关键字在`TypeScript`的高级类型操作中出现频率较高，主要使用场景有：接口继承 类型约束以及条件类型，这里使用的是其作为条件类型的情况，通常与`infer`关键字一起使用。
```ts
type MyReturnType<T> =  T extends (...args: any) => infer R ? R : T
```
代码详解：
- `T extends (...args: any) => infer R`：判断`T`类型是否是一个函数的子类型，即`T`是不是一个函数。
- `infer R`：表示待推导的函数返回类型为`R`，后续可以在表达式中使用`R`来代替真正的返回类型。

## 更多
[TypeScript类型体操中的关键字详解（一）](https://juejin.cn/post/7105778922851139598)

[TypeScript类型体操中的关键字详解（二）](https://juejin.cn/post/7106038466139389959#heading-0)

[TypeScript类型体操中的关键字详解（三）](https://juejin.cn/post/7106396908448382990)

[理清 TypeScript 的 interface 与 type 的区别](https://juejin.cn/post/7093132160747438117)