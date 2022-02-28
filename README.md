
# typescript 学习笔记
##  typescript概述

**JS开发中的问题**

- 使用了不存在的变量、函数或成员
- 把一个不确定的类型当作一个确定的类型处理
- 在使用null或undefined的成员

**js的原罪**

- js语言本身的特性，决定了该语言无法适应大型的复杂的项目
- 弱类型：某个变量，可以随时更换类型。
- 解释性：错误发生的时间，是在运行时

前端开发中，大部分的时间都是在排错
**typescript**  
简称ts
ts是js的超集，也就是说在ts文件里可以书写js代码
无论是浏览器环境，还是node环境，无法直接识别ts代码
> babel: es6 -> es5

> tsc: ts -> es

tsc: ts编译器

静态：类型检查发生的时间，在编译的时候，而非运行时
> 官网：http://www.typescriptlang.org/

> 中文网：https://www.tslang.cn/  个人翻译

##  在node环境中使用ts
全局安装typescript
```
> npm install -g typescript
```
先来试一下，新建一个ts文件，index.ts
```ts
let str:string = "holle world";
console.log(str);
```
在同目录执行命令
```
> tsc index.ts
```
然后就会在同目录生成index.js文件，这一步将ts转化成我们熟悉的js
```js
var str = "holle world";
console.log(str);
```
新建一个配置文件tsconfig.json

![biWIET.png](https://s4.ax1x.com/2022/02/24/biWIET.png)

可以在这个文件里面配置文件的转化规则
```json
{
    "compilerOptions": { //编译选项
        "target": "es2016", //配置编译目标代码的版本标准
        "module": "commonjs", //配置编译目标使用的模块化标准
        "lib": ["es6"],  //转化成es6
        "outDir": "./dist" //输出目录
    },
    "include": ["./src"]//输入目录
}
```
直接在项目根目录输入
```
> tsc 
```
就可以在`./dist`目录下看到转化后的文件
**第三方库简化操作**
ts-node: 将ts代码在内存中完成编译，同时完成运行(全局安装)

nodemon: 用于检测文件的变化（全局安装）
分别安装全局安装
```
> npm i ts-node -g
```
```
> npm i nodemon -g
```
新建package.json文件进行配置，跟tsconfig.json同目录
```json
{
    "scripts": {
        "dev": "nodemon --watch src -e ts --exec ts-node src/index.ts"
    },    
    "devDependencies": {
        "@types/node": "^11.13.7"
    }
}
```
@types/node:可以在ts中执行一些node api 例如console.log,不加`@types/node`，在ts中执行console.log就会报错
然后分别在命令行执行以下命令
```
> npm i
```
```
> npm run dev
```
就可以边写边看效果啦

**其他注意事项**
@types/node

@types是一个ts官方的类型库，其中包含了很多对js代码的类型描述。

> JQuery：用js写的，没有类型检查
> 安装@types/jquery，为jquery库添加类型定义

##  基本类型约束使用
**基本类型**

- number：数字
- string：字符串
- boolean：布尔
- 数组
- object: 对象
- null 和 undefined

null和undefined是所有其他类型的子类型，它们可以赋值给其他类型

通过在配置文件tsconfig.json编译选项中添加```strictNullChecks:true```，可以获得更严格的控类型检查，null和undefined只能赋值给自身。

**如何使用**
在声明变量时加上类型，这样在以后重新赋值的时候就只能用此类型的数据进行赋值（赋值null或者undefined也是可以的在非严格控类的情况下）
例如
```ts
let str:String = 'abc'
console.log(str)
str = 1
```
报错
![biODQf.md.png](https://s4.ax1x.com/2022/02/24/biODQf.md.png)

以下是正确的写法
```ts
let str:String = 'abc'
console.log(str)
str = '1'
str = null //在非严格控类情况下可以这样赋值
str = undefined //在非严格控类情况下可以这样赋值
```
##  常见类型 一下都是严格控类情况下
注：string,number,boolean,object的类型名称的首字母可大写可小写，其他的类型名称首字母只能小写
```ts
let str:String = 'abc' //只能赋值字符串
let num:Number = 1 //只能赋值数字(NaN和Infinity也可以)
let flag:Boolean = false //只能赋值true或false
let empty:null = null //只能赋值null
let notDefined:undefined = undefined //只能赋值undefined
```
object类型，可以赋值typeof返回为object的一切数据类型，除了null
```ts
let obj:object ={}//可以赋值
obj = []//可以赋值
obj = function(){}//可以赋值
// obj = null  这样赋值会报错
```
```ts
let allType:any = 'all type' //可以赋值任意类型

//数组每一项的约束有两种方式，每一项都必须为number
let nums1: number[] = [3, 4, 5]
let nums2: Array<number> = [3, 4, 5];
```
`|`表示或者的意思，可以给一个变量赋多种类型
```ts
let name:string|undefined = 'lby' //只能赋值字符串或者undefined
name = undefined //可以正常赋值
name = 'buouyu' //可以正常赋值

let a:number|undefined|null = 1
a = undefined
a = null

//以下这种方式数组的每一项只能全部为数字，或者全部为字符串
let arr1:number[]|string[] =[1,2,3]//不能赋值['a',1,2]这样既有数字又有字符串的数组

//以下这种方式数组的每一项可以为数字或者字符串
let arr2:Array<number|string> = ['a',1,2]

let tu: [string, number];
tu = ["3",4];//必须为每项对应的类型，不能多值或者少值，也就是说此时数组的length只能为2

let user: {
    name:string
    age:number
} = {
    name:"buouyu",
    age:21,
    // id:1  不能多加属性或者减少属性
}


```
**指定值**
```ts

let gender: "男" | "女";//只能赋值男或者女

gender = "女";//可以赋值
gender = "男";//可以赋值
// gender = "未知" //报错

let arr: []; //arr永远只能取值为一个空数组


```

**类型别名**
对已知的一些类型定义名称
```
type 类型名 = ...
```
```ts
type Gender = "男" | "女" //自定义一个Gender类型，这个类型只能赋值男或者女
type User = { //自定义一个User类型
    name:string
    age:number
    gender:Gender
}

let u:User

u = {
    name:"buouyu",
    gender:"男",
    age:21
}
//自定义的类型和其他的类型用法一样
```

**关于函数**
```ts

//传参必须为数字
function fn1(n: number) {
    // return n % 2 === 0;
    console.log(n)
}
fn1(3)//必须写数字,也不能不写

//可选参数：可以在某些参数名后加上问号，表示该参数可以不用传递。可选参数必须在参数列表的末尾。
function fn10(n?:number){
}
fn10()//可以不用传参 或者传数字

//类型写在函数括号外面，函数只能返回这个类型
function fn2(obj:object):object{
    // return 1  //return其他类型会报错
    return {
        name:"buouyu",
        age:21
    }
}

//返回的类型必须为指定每项类型的数组
function fn3():Array<number>{
    // return ['1',2] 不能这样return
    return [1,2,3] //每项必须为数字
}

function fn30():[number,string]{
    return [1,'a']
}

//可以返回任意类型
function fn4():any{
    //没有return就是 return undefined
}

//void类型：通常用于约束函数的返回值，表示该函数没有任何返回
function fn5(obj:object):void{
    // return 2; 写return会报错，这个函数不用有返回值
}

//never类型：通常用于约束函数的返回值，表示该函数永远不可能结束
function fn6(obj:object,msg:string):never{
    //这个函数会一直执行
    while (true) {
        // break 不能加这个，加break也会报错
    }
    // throw new Error(msg)  或者执行中报错
}
```

##  扩展类型
###  枚举
枚举通常用于约束某个变量的取值范围
```
enum 枚举名{
    枚举字段1 = 值1,
    枚举字段2 = 值2,
    ...
}

```
枚举会出现在编译结果中，编译结果中表现为对象。

枚举的字段值可以是字符串或数字
`为什么要用枚举`
- 在类型约束位置，会产生重复代码。可以使用类型别名解决该问题。
- 逻辑含义和真实的值产生了混淆，会导致当修改真实值的时候，产生大量的修改。
- 字面量类型不会进入到编译结果。

**数字枚举**
```ts
enum Level {
    level1,
    level2,
    level3
}

let l: Level = Level.level1; //l只能赋值为数字
l = Level.level2;
console.log(l);//打印结果为1
```
如果像这样枚举字段不赋任何值，就默认
第一个枚举字段level1为0
第二个枚举字段level2为1
第三个枚举字段level3为3     
依次递增……
`数字枚举的值会自动自增`
```ts
enum Level {//自增
    level1=5,
    level2,//6
    level3//7
}

let l: Level = Level.level1;
l = Level.level2;
console.log(l);//结果为6
```
`被数字枚举约束的变量，可以直接赋值为数字`
```ts
enum Level {
    level1=9,
    level2=5,
    level3//6
}

let l: Level = Level.level1;
l = Level.level2;
console.log(l);//结果为5
l = 7;
console.log(l);//结果为7

function getUsers(lev:Level){
    console.log(lev)//结果为6
}
getUsers(Level.level3)
```
**字符串枚举**
```ts
enum Direction {//可以把它类比为对象
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}

let position:Direction = Direction.Down//只能赋值Direction里面的枚举字段
// position ='buouyu'//这样赋值会报错
// position = 'UP' //这样赋值会报错
position=Direction.Right //可以这样赋值
```
**异构枚举**（不建议这样用）
```ts
enum Heterogeneous {
    No = 0,
    Yes = "YES",
}

let isTrue:Heterogeneous = Heterogeneous.No;//只能赋值数字或者枚举里面值为字符串的字段
isTrue =1//可以赋值
// isTrue = 'a'//这样赋值会报错
isTrue = Heterogeneous.Yes//可以赋值

let isFalse:Heterogeneous = Heterogeneous.Yes;//只能赋值数字或者枚举里面值为字符串的字段
// isFalse = 'No'//这样赋值会报错
// isFalse = 'YES'//这样赋值会报错
isFalse = 1//可以赋值
isFalse = Heterogeneous.No//可以赋值
```
**扩展知识：位枚举（枚举的位运算）**（新手可以不看）

针对的数字枚举

位运算：两个数字换算成二进制后进行的运算
[关于位运算的详解](https://blog.csdn.net/zhaoyunfei1/article/details/95312550)：https://blog.csdn.net/zhaoyunfei1/article/details/95312550
也可以自行百度

```ts
enum Permission {
    Read = 1,   // 0001
    Write = 2,  // 0010
    Create = 4, // 0100
    Delete = 8  // 1000
}

//1. 如何组合权限
//使用或运算
//0001
//或
//0010
//0011
let p: Permission = Permission.Read | Permission.Write;

//2. 如何判断是否拥有某个权限
//0011
//且
//0010
//0010
function hasPermission(target: Permission, per: Permission) {
    return (target & per) === per;
}
//判断变量p是否拥有可读权限

//3. 如何删除某个权限
//0011
//异或
//0010
//0001
p = p ^ Permission.Write;
console.log(hasPermission(p, Permission.Write));
```

## 




