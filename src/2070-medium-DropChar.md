## 题目

从字符串中剔除指定字符。

例如：

```ts
type Butterfly = DropChar<" b u t t e r f l y ! ", " ">; // 'butterfly!'
```

## 解答

为了解决这个问题，首先需要了解什么是**模板字面量**类型？

模板字面量类型以[字符串字面量类型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types)为基础，可以通过联合类型扩展成多个字符串。

它们跟 JavaScript 的模板字符串是相同的语法，但是只能用在类型操作中。当使用模板字面量类型时，它会替换模板中的变量，返回一个新的字符串字面量：

```ts
type World = "world";
 
type Greeting = `hello ${World}`;
// type Greeting = "hello world"
```

使用模板字面量类型时，我们可以从字符串中推断所需的部分, 在下方的代码中我们用`L`和`R`推断字符串的左侧部分和右侧部分。它们之间的分界符是所需的字符本身，即`C`。

```ts
type DropChar<S, C> = S extends `${infer L}${C}${infer R}` ? never : never;
```

使用这样的表示方法，我们会得到一个编译错误`Type ‘C’ is not assignable to type ‘string | number | bigint | boolean | null | undefined’.`因此需要添加一个泛型约束去修复它。

```ts
type DropChar<S, C extends string> = S extends `${infer L}${C}${infer R}`
  ? never
  : never;
```

当`S`的结构符合`${infer L}${C}${infer R}`时，即`S`可以赋值给`${infer L}${C}${infer R}`,我们将`C`剔除，返回`${L}${R}`

```ts
type DropChar<S, C extends string> = S extends `${infer L}${C}${infer R}`
  ? `${L}${R}`
  : never;
```
但返回的`${L}${R}`可能仍然含有`C`,因此我们需要递归地调用该类型，知道剔除所有`C`。

```ts
type DropChar<S, C extends string> = S extends `${infer L}${C}${infer R}`
  ? DropChar<`${L}${R}`, C>
  : never;
```
当目标字符串中不含有`C`时，直接返回字符串本身`S`,这同时也是递归结束的条件。

```ts
type DropChar<S, C extends string> = S extends `${infer L}${C}${infer R}`
  ? DropChar<`${L}${R}`, C>
  : S;
```

## 更多
- [extends关键字详解](https://juejin.cn/post/7106038466139389959#heading-3)
- [infer关键字详解](https://juejin.cn/post/7106396908448382990#heading-1)
-   [Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)
-   [Inferring within Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#inferring-within-conditional-types)