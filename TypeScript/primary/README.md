# 变量

**let，const的暂时性死区**
虽然这些变量始终“存在”于它们的作用域里，但在直到声明它的代码之前的区域都属于 暂时性死区
（它只是用来说明我们不能在 let语句之前访问它们）

```ts
function foo() {
    // okay to capture 'a'
    return a;
}

// 不能在'a'被声明前调用'foo'
// 运行时应该抛出错误
foo();

let a;
```

> 注意一点，我们仍然可以在一个拥有块作用域变量被声明前获取它。 只是我们不能在变量声明前去调用那个函数。 如果生成代码目标为ES2015，现代的运行时会抛出一个错误；然而，现今TypeScript是不会报错的。

但是ts编译后的代码，直接把let替换成了var，事实上并不会报错。因为var变量的“变量提升”


**重定义和屏蔽**

重定义是指在一个作用域里，例如函数作用域里，重复声明变量，无论是函数参数，或是声明var变量。

但只要是不同的块内，就可以重复声明这个变量，这一点叫做屏蔽。
编译前和编译后的源码：

```js
function sheild () {
  let y = 0
  for(let y = 0; y < 10; y++) {
    console.log(y)
  }
  return y
}

function sheild() {
    var y = 0;
    for (var y_1 = 0; y_1 < 10; y_1++) {
        console.log(y_1);
    }
    return y;
}
```


**解构**


解构实际上就是通过字面量对象或数组的结构，去赋值给变量。
我没想到的是，还可以通过类似稀疏数组的声明来解构。

```ts
let array = [1, 2, 3, 4];
let [, b, , c] = array;
```

**null和undefined**

> 默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。

> 当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。


**类型断言**
类型断言只有两种语法：
* ```let strLength: number = (<string>someValue).length;```
* ```let strLength: number = (someValue as string).length;```

**显式赋值断言**

[更严格的类属性检查](https://www.tslang.cn/docs/release-notes/typescript-2.7.html)

> TypeScript 2.7引入了一个新的控制严格性的标记 --strictPropertyInitialization！

使用这个标记会确保类的每个实例属性都会在构造函数里或使用属性初始化器赋值。 在某种意义上，它会明确地进行从变量到类的实例属性的赋值检查。

```ts
class car {
  size!: number
}
```