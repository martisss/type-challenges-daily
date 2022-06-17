## 题目
实现一个通用`Last<T>`，它接受一个数组`T`并返回其最后一个元素的类型。

例如

```ts
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type tail1 = Last<arr1> // expected to be 'c'
type tail2 = Last<arr2> // expected to be 1
```
## 解答
`Last`类型的实现这里介绍两种方法，都比较巧妙，一开始可能想不到，但是看过之后其实很容易理解，一种方式是利用索引，另外一种方式是使用`infer`推导占位的方式实现，之前的文章中也有关于`infer`关键字的用法的[详细解析](https://juejin.cn/post/7106396908448382990)。
### 解法一：索引方式
代码详解：
```ts
type Last<T extends any[]> = [any, ...T][T['length']]
```
本身`T`的长度是`T['length']`, 前面加上一个`any`,再利用`...`解构`T`，那么新数组`[any, ...T]`的第`T['length']`个元素即是数组`T`的最后一个元素的类型。

可以用以下伪代码辅助理解：
```ts
// 原数组
const T = [1, 2, 3]
// 新数组
const arr = [any, 1, 2, 3]
// 结果: 3
const result = arr[T['length']]
```
`T['length']`：表示原始`T`数组的长度，例如`[1, 2, 3]`，长度值为`3`。而在新数组中，索引为`3`的位置正好是最后一个元素的索引。
### 解法二：`infer`占位推导

代码详解：
```ts
type Last<T extends any[]> = T extends [...infer R, infer L] ? L : never
```
- 使用`T extends any[]`对`T`进行类型约束，`T`必须是一个数组，具体`内容`是啥我们不关心
- 使用`L`对原数组中最后一个元素进行占位，而其它元素我们用一个`R`数组表示。
- 再用`extends`条件语句进行判断，如果数组满足`[...infer R, infer L]`这种格式，就返回最后一个元素。
## 更多
-   [Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)
-   [Type inference in conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#inferring-within-conditional-types)
-   [Variadic Tuple Types](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-0.html#variadic-tuple-types)
- [infer关键字详解](https://juejin.cn/post/7106396908448382990)