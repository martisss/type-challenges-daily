## 题目
实现一个通用`MyReadonly2<T, K>`，它带有两种类型的参数`T`和`K`。

`K`指定为`T`的属性的子集，对应的属性是只读（`readonly`）的。如果未提供`K`，则应使所有属性都变为只读，就像普通的`Readonly<T>`一样。

例如

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

const todo: MyReadonly2<Todo, 'title' | 'description'> = {
  title: "Hey",
  description: "foobar",
  completed: false,
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
todo.completed = true // OK
```
## 解答
### 回顾`<Readonly>`
要回答这个问题，首先需要回顾下`Readonly`类型的实现：
`Readonly`是用来让所有属性变为只读，其用法为：

```ts
type Person = {
  readonly name: string;
  age: number;
}

// 结果：{ readonly name: string; readonly age: number; }
type ReadonlyResult = MyReadonly<Person>
```

实现方式
```ts
type MyReadonly<T> = {
  readonly [P in keyof T]: T[P]
}
```
用`in`关键字遍历`T`的属性所组成的联合类型`keyof T`，结合`readonly`关键字，将每个属性设置为只读的。

### `<MyReadonly2>`实现思路
那么如何实现`MyReadonly2`呢？它是`Readonly`的进阶版本：可选的`Readonly`,将给定的属性设置为`readonly`,其他属性不变，如果没有给定要设置的属性，就默认将所有属性变为`readonly`,同`<Readonly>`类型。
根据上述分析：
首先对`K`进行限定，`K`是`typeof T`，同时没有给定`K`时，默认`K`就是`keyof T`。
然后遍历`K`中的每一个属性，将其设定为只读的。
```ts
type MyReadonly2<T, K extends keyof T> = T & { readonly [P in K]: T[P] };
```
这种解法在`ts v4.4+`版本中能够正常工作，但是在`ts v4.5+`版本中就会报错，截至目前`ts Playground`显示的最新版本号是`4.7.2`.
直接给出修改后的解法：
```ts
type MyReadonly2<T, K extends keyof T> = Omit<T, K> & { readonly [P in K]: T[P] };
```
区别在于将原来解法中的`T`换成了`Omit<T, K>`,将要设置为`readonly`的对应属性剔除，然后再和设置为`readonly`的部分组成联合类型。
## 更多
-   [Intersection types](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types)
-   [Mapped Types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)
-   [Index Types](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)
-   [Using type parameters in generic constraints](https://www.typescriptlang.org/docs/handbook/2/generics.html#using-type-parameters-in-generic-constraints)