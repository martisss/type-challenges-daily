## 题目

不使用 `Omit` 实现 TypeScript 的 `Omit<T, K>` 泛型。

`Omit` 会创建一个省略 `K` 中字段的 `T` 对象。

例如：

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyOmit<Todo, 'description' | 'title'>

const todo: TodoPreview = {
  completed: false,
}
```

## 解答

### 解法一

根据内置的工具类型的`Exclude`和`Pick`，可以实现`Omit`类型

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>
```

首先，删除指定字段，字段类型限定在 `string | symbol number`中(使用`K extends keyof any`)，然后用`Exclude`从`T`的属性所组成的字面量联合类型中移除指定字段，形成新的联合类型；最后利用`Pick`选取剩下的字段生成新的类型。

### 解法二

在这里我们需要返回一个新的对象类型，我们可以使用映射类型（Mapped Types）达到这样的效果，映射类型建立在索引签名的语法之上，用于声明未提前声明的属性类型，在很多地方你都可以看到这样的表达：`[P in keyof T]: T[P]`，这就是在使用映射类型，遍历`T`的属性，映射到`P`上，`P`作为新对象类型的键，`T[P]`作为新对象类型的值。

## 更多

- [extends关键字详解](https://juejin.cn/post/7106038466139389959#heading-3)
- [infer关键字详解](https://juejin.cn/post/7106396908448382990#heading-1)
-   [Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)
-   [Inferring within Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#inferring-within-conditional-types)