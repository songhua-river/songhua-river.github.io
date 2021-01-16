---
layout: post
title: TypeScript
categories:
  - JavaScript
  - 基础
tags:
  - TypeScript
abbrlink: d044eab7
date: 2021-01-14 23:26:07
---

#### TS 是什么

是JavaScript的超集，添加了类型系统。 拟补JavaScript在开发大型系统时的不足。

#### 相比 JS 的优势

+ 类型化的思维方式，开发更加严谨，提前发现错误，减少改bug的时间

+ 类型系统提高了代码的可读性，并使维护和重构更加容易

+ 补充了接口枚举等开发大型应用时JS缺失的功能。

#### TS 解析工具

通过`tsc` 命令把 TS 转为 JS 代码

```bash
yarn add typescript
```

#### helloWord

创建 `index.ts`

配置 `package.json`

```json
"scripts": {
  "tsc": "tsc src/index.ts &&  node src/index.js"
}
```

执行 `yarn tsc`

使用自动化的方法,通过`tsc --init `创建tsconfig.json

配置输入输出目录 `rootDir` `outDir`

在 VScode 中点击 Terminal -> Run Task -> typescript 选择 tsconfig.json 配置文件

可以实时监听文件变化并输出


#### 语法

##### 注释

单行注释 `//` 快捷键 `ctrl + /`

多行注释 `/*  */` 快捷键 `ctrl + shift + A` (ubuntu)

##### 类型注解

为变量添加类型约束的方式

声明变量必须指定type类型

```javascript
let a:number;
```

##### 变量命名规则

不能以数字开头，且只能包含 `$`，`_`， `数字`，`字母`


##### 数据类型

原始类型: Number/String/Bollean/undefined/null 对应类型注解为 number/string/boolean/undefined/null

**undefined null类型 属于其他类型 never 的子类型**

```javascript
const n:number | null | undefined;

n = 1;
```

**算术运算符只能和数字类型一起使用**


**数组类型 Array**

```javascript
// 指定数组中的类型
let arr:number[] = [1,2,3]
```

```javascript
let arr:Array<number> = [1,2,3]
```

```javascript
let arr:any[] = [1,2,3]
```

**元组类型 tuple**

数组类型的一种，可以指定数组中不同元素的类型

```javascript
let arr:[string，number] = ['123',3.4]
```

**枚举类型 enum**

定义枚举类型

```javascript
enum StatusEnum {
  success = 0,
  error = -1
}

const a: StatusEnum = StatusEnum.success
```

如果没有声明值，则返回枚举字段的索引， 如果其中一个有值，后面没有赋值的字段会把前面的值 +1 返回

```javascript
enum Color { red, gray = 5, black}

const c:Color = Color.red //0
const c1:Color = Color.gray //5
const c2:Color = Color.black //6
```

**任意类型 any**

```javascript
const a:any = document.getElementById('app')
a.style.innerHTML = '<div />'
```

**void 类型**

void 表示没有任何类型，一般用于定义方法的时候没有返回值

```javascript
function fun:void(){
  cosnole.log(1)
}

const fun = (): void => {
  console.log(1)
}
```

**never类型**

包括 `null` 和 `undefined` 类型，代表从不会出现的值，这意意味着never类型只能被never类型所赋值

```javascript
const a: never = (() => { throw new Error })()
```

#### 定义函数

函数参数和返回值类型必须被声明

```javascript
function fn(a: number = 2, b?: number, ...c: Array<any>): void {
  if (b) {
    console.log(a, b, c);
  } else {
    console.log(a, b, c);
  }
}

fn(1)
```

函数重载表示同名的函数如果参数不同函数会重载

但是JS中没有重载的概念，下面同名的函数会覆盖上面的函数

TS中模拟函数重载, 通过不同的参数类型校验

```javascript
function fn(a: number): number;
function fn(b: string): string;
function fn(c: any): any {
  if (typeof c === 'number') {
    return c + 1;
  }
};
```


#### 定义类

```javascript
class C {
  constructor(n: string) { // 构造函数，实例化类的时候触发的方法
    this.name = n;
  }
  name: string; // 定义属性 省略前面的public关键字

  run():void{ // 定义方法
    console.log('void')
  }

}
```

TS提供了三种属性的修饰符

public: 公有 在类，子类 类外面都可以访问

protected: 保护类型 在类里面，子类里面可以访问，在类外部没法访问

private: 私有 在类里面可以方法，子类，类外都不能访问

属性如果不加修饰符就是共有

