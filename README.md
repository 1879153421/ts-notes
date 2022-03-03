
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
[更多配置](https://blog.csdn.net/weixin_39843414/article/details/107724369)
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
    age:20,
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
    age:20
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
        age:20
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

### 接口 inteface

**接口的简单使用**
```ts
//接口约束对象
interface User {
    name: string
    age: number
    sayHello(): number
    //sayHello后面的number：
    //约束函数返回值，接口这里约束函数参数是没有作用的，
    //而且如果是void，函数再用的时候是可以有返回值的相当于any,
    //如果是any也可以没有返回值
}

let u:User = {
    name:'buouyu',
    age:20,
    sayHello(){
        console.log('hello')
        return 1
    }
}
```
相当于
```ts
type User = {
    name: string
    age: number
    sayHello: () => number
}
let u:User = {
    name:'buouyu',
    age:20,
    sayHello(){
        console.log('hello')
        return 1
    }
}
```
约束函数
```ts
//接口约束函数
interface Condition {
    (n: number): boolean//正常约束参数，约束返回值
}
function sum(a:number,b:number,callBakc:Condition):number{
    callBakc(a)
    return a+b
}
sum(1,2,(a)=>a>0)

//类型别名约束函数
// type Condition = (n: number) => boolean
```
联合约束
```ts
interface RunOptions { 
    program:string; 
    commandline:string[]|string|(()=>string); //可以是这三种的任意一种情况
} 
```
接口和数组
```ts
interface namelist { 
   [index:number]:string //索引是数字，元素是字符串
} 
 
var list2:namelist = ["John",1,"Bran"] // 错误元素 1 不是 string 类型
interface ages { 
   [index:string]:number //索引是字符串，元素是数字
} 
 
var agelist:ages; 
agelist["John"] = 15   // 正确 
agelist[2] = "nine"   // 错误 
```
**接口的继承**

```ts
interface A {
    T1: string
}

interface B {
    T2: number
}

interface C extends A, B {
    T3: boolean
    // T2:string //会报错，不能跟父级的重复

}

//相当于
interface C{
    T1: string
    T2: number
    T3: boolean
}
```
用类型别名实现类似的功能，叫做交叉类型
```ts
type A = {
    T1: string
}

type B = {
    T2: number
}

type C = {
    // T1: number  //会报错，不能和后面&的重复
    T3: boolean
} & A & B
```
**readonly**
```ts
type User = {
    readonly id: string //只读属性
    name: string
    age: number,
    readonly arr: readonly string[] //只读属性
}

const person:User = {
    id:'handsome',
    name:'buouyu',
    age:20,
    arr:['yes','yes','yes']
}
// person.id="ugly" //会报错
// person.arr.push('yes') //会报错
// person.arr[0]="no"//会报错
```

**其他**
```ts
interface User {
    name?: string// 后面有？代表这个属性可有可无,
    age: number
}
const u:User ={
    age:20,
    // name:'buouyu'//可写可不写
}
```
<!-- **类型兼容性** -->
<!-- 参数：传递给目标函数的参数可以少，但不可以多

返回值：要求返回必须返回；不要求返回，你随意； -->

### ts中的类
> 面向对象思想

访问修饰符可以控制类中的某个成员的访问权限

- public：默认的访问修饰符，公开的，所有的代码均可访问
- private：私有的，只有在类中可以访问
- protected：受保护的成员，只能在自身和子类中访问

```strictPropertyInitialization:true```
此规则将验证构造函数内部初始化前后已定义的属性。
必须要确保每个实例的属性都有初始值，可以在构造函数里或者属性定义时赋值。

```ts
class User {
    readonly id: number //只读属性不能改变
    gender: "男" | "女" = "男"//或男或女，默认为男
    pid?: string//可有可无
    private _publishNumber: number = 3; //私有属性，不能被外界访问到，默认值为3

    constructor(public name: string, private _age: number) {//构造器，
        this.id = Math.random();
    }
    set age(value: number) {
            this._age = value;//注意这个用this.age,会递归调用age
    }
    get age() {
        return this._age
    }

    publish(title: string) {
       this._publishNumber ++;
       console.log(title,this._publishNumber)
    }
}
const u = new User("buouyu", 20);
console.log(u) //返回
// User {
//     name: 'buouyu',
//     _age: 20,
//     gender: '男',
//     _publishNumber: 3,
//     id: 0.6268206795962001
//   }

console.log(Object.getOwnPropertyDescriptor(u,'id'))
// {
//     value: 0.20637463418090052,
//     writable: true,
//     enumerable: true,
//     configurable: true
//   }

// u.id = '112233' //报错，虽然描述符里面显示的可写，可遍历，但还是不能更改，会报错
```
**继承，private，protected**
子类成员不能改变父类成员的类型

无论是属性还是方法，子类都可以对父类的相应成员进行重写，但是重写时，需要保证类型的匹配
```ts
export class Tank {
    protected name: string = "坦克"//只能在本类和子类中使用，不能在外部使用
    protected shape:string = 'rectangular'
     color='red'//直接赋值会自动检测类型
    private size = 10//私有属性，只能在Tank类中使用，不能在子类或外部使用
    sayHello() {
        console.log(`我是一个${this.name}`)
    }
}
export class PlayerTank extends Tank {//继承
    name: string = "玩家坦克"//重新赋值，此时的name已经不受保护，可以被外界访问
    life: number = 5;
    // color=3; //报错，需要赋值字符串
    color='blue'
    // size = 100 //会报错
    sayHello() {
        console.log("啦啦啦啦");
    }

    test() {
        super.sayHello();//我是一个玩家坦克
        this.sayHello();//啦啦啦啦
        console.log(super.name)//undefined
        console.log(this.name)//玩家坦克
        console.log(super.color)//undefined
        // console.log(this.size) //报错
    }
}
const p = new PlayerTank()

console.log(p)
// PlayerTank {
//     name: '玩家坦克',
//     shape: 'rectangular',
//     color: 'blue',
//     size: 10,
//     life: 5
//   }
console.log(p.name)//玩家坦克
// console.log(p.shape)//报错
// console.log(p.size) //报错
p.test()

```
**抽象类**
有时，某个类只表示一个抽象概念，主要用于提取子类共有的成员，而不能直接创建它的对象。该类可以作为抽象类。

给类前面加上```abstract```，表示该类是一个抽象类，不可以创建一个抽象类的对象。

**抽象成员**

父类中，可能知道有些成员是必须存在的，但是不知道该成员的值或实现是什么，因此，需要有一种强约束，让继承该类的子类，必须要实现该成员。

抽象类中可以有抽象成员，这些抽象成员必须在子类中实现

```ts
abstract class Chess {
    x: number = 0
    y: number = 0

    abstract readonly name: string;//抽象成员

    protected abstract rule(targetX: number, targetY: number): boolean;//抽象成员
}

class Horse extends Chess {
    protected rule(targetX: number, targetY: number): boolean {
        return true;
    }

    readonly name: string = "马";//子类中必须实现父类的抽象成员
}

const h = new Horse()
// const c = new Chess()  //报错，不能new抽象类
```
**静态成员**
静态成员是指，附着在类上的成员（属于某个构造函数的成员）

使用static修饰的成员，是静态成员
```ts
class A {
    name='buouyu'
    static age:number=20//静态成员不在this对象里
}
const a = new A()
console.log(a)//A { name: 'buouyu' }
console.log(A.age)//20
```
**再谈接口**
接口用于约束类、对象、函数，是一个类型契约。

接口和类型别名的最大区别：接口可以被类实现，而类型别名不可以

接口可以继承类，表示该类的所有成员都在接口中。
>类型保护函数：通过调用该函数，会触发TS的类型保护，该函数必须返回boolean
```ts
class A {
    a1: string = ""
    
}
class B {
    b1: number = 0;
}
//接口继承类
interface C extends A, B  {
 }

const c: C = {
    a1: "",
    b1: 0,
}
```
```ts
interface IFireShow {//会火圈表演的动物都会遵循该接口
    singleFire(): void;
}
//类型保护函数
//判断一个对象是否遵循该接口
//也就是判断一个动物是否会火圈表演
function hasFireShow(ani: object): ani is IFireShow {
    if ((ani as IFireShow).singleFire ) {
        return true;
    }
    return false;
}

 abstract class Animal {
    abstract type: string;

    constructor(
        public name: string,
        public age: number
    ) {

    }
}

 class Lion extends Animal {
    type: string = "狮子";

}
//implements是对某个接口的实现，必须满足接口的类型规范,这里Tiger类必须满足IFireShow规范
 class Tiger extends Animal implements IFireShow {
    type: string = "老虎";

    singleFire() {//这个成员不能少，需满足接口IFireShow规范
        console.log(`${this.name}穿越了单火圈`);
    }
}

const animals: Animal[] = [
    new Lion("王富贵", 12),
    new Tiger("坤坤", 20),
    
];

//所有会进行火圈表演的动物，完成火圈表演
animals.forEach(a => {
    if (hasFireShow(a)) {
        a.singleFire();
    }
})
```
**索引器**

```对象[值]```，使用成员表达式

在TS中，默认情况下，不对索引器（成员表达式）做严格的类型检查

使用配置```noImplicitAny:true```开启对隐式any的检查。

隐式any：TS根据实际情况推导出的any类型

在索引器中，键的类型可以是字符串，也可以是数字

在类中，索引器书写的位置应该是所有成员之前

TS中索引器的作用

- 在严格的检查下，可以实现为类动态增加成员
- 可以实现动态的操作类成员

在JS中，所有的成员名本质上，都是字符串，如果使用数字作为成员名，会自动转换为字符串。

在TS中，如果某个类中使用了两种类型的索引器，要求两种索引器的值类型必须匹配
```ts
interface Person{
     name:string
}
const obj:Person = {
    name:'buouyu'
}
// obj.age=20 //报错
obj['age']=20 //不报错，若开启"noImplicitAny": true 就报错了
```
```ts
class M {
    [prop:string]:object|number//索引器书写的位置应该是所有成员之前
    //成员名为number或string类型都会检测
    // name="buouyu"//报错，成员必须赋值为对象或number
    user={
        name:'buouyu'
    }
    number=20
    0=1
    // 1=true //报错
}
const m = new M()
console.log(m)
//M { '0': 1, user: { name: 'buouyu' }, number: 20 }
class MyArr {
    [prop:number]:number|object//索引器书写的位置应该是所有成员之前
    //只检测成员名为number类型的成员
    name="buouyu"//不报错，因为没检测
    age=true//不报错
    0=1
    1=2
    2={
        name:'buouyu'
    }
    // 3='buouyu'//报错
}
const myArr = new MyArr()
console.log(myArr)
// MyArr {
//     '0': 1,
//     '1': 2,
//     '2': { name: 'buouyu' },
//     name: 'buouyu',
//     age: true
//   }
```
如果某个类中使用了两种类型的索引器，要求两种索引器的值类型必须匹配
![bYc3uj.png](https://s4.ax1x.com/2022/03/03/bYc3uj.png)

**this指向约束**
在JS中this指向的几种情况:

明确：大部分时候，this的指向取决于函数的调用方式

- 如果直接调用函数（全局调用），this指向全局对象或undefined (启用严格模式)
- 如果使用```对象.方法```调用，this指向对象本身
- 如果是dom事件的处理函数，this指向事件处理对象

特殊情况：

- 箭头函数，this在函数声明时确定指向，指向函数位置的this
- 使用bind、apply、call手动绑定this对象

TS中的this:
配置noImplicitThis为true，表示不允许this隐式的指向any

在TS中，允许在书写函数时，手动声明该函数中this的指向，将this作为函数的第一个参数，该参数只用于约束this，并不是真正的参数，也不会出现在编译结果中。

没有约束this
```ts
interface IUser {
    name: string,
    sayHello(): void
}

const u: IUser = {
    name: "buouyu",
    sayHello() {
        console.log(this.name)
    }
}
const say = u.sayHello;
say()//undefined ，因为this指向window
u.sayHello()//buouyu
```
约束了this
```ts
interface IUser {
    name: string,
    sayHello(this: IUser): void//约束了this
}

const u: IUser = {
    name: "buouyu",
    sayHello() {
        console.log(this.name)
    }
}
const say = u.sayHello;
// say() //这样直接执行会报错
u.sayHello()//this指向正确
```
## 泛型
**ts为什么要有泛型？**
先举一个简单的例子
```ts
//我们实现一个功能，传入字符串，我们就return一个字符串，传入一个数字我们就return一个数字
function fn(a:string|number):number|string{
    return a
}

//在我们传入数字时，就应该知道result是一个数字，但实际result类型为数字或字符串
let result =  fn(1)
result ="a" //可以正常赋值，但我们只希望他只能赋值为数字
```
![b8tWvD.md.png](https://s4.ax1x.com/2022/03/02/b8tWvD.md.png)

**使用泛型**
```ts
//这里尖括号里面的T，就相当于一个类型变量，会根据我们传的值自动推导类型
//比如我们传的数字，那么T就代表number类型
function fn<T>(a:T):T{
    return a
}

let result =  fn(1) //这里就很确定result是一个数字类型
// result ="a" //报错
```
![b8wt6f.md.png](https://s4.ax1x.com/2022/03/02/b8wt6f.md.png)

**泛型**：是指附属于函数、类、接口、类型别名之上的类型

泛型相当于是一个类型变量，在定义时，无法预先知道具体的类型，可以用该变量来代替，只有到调用时，才能确定它的类型

很多时候，TS会智能的根据传递的参数，推导出泛型的具体类型

如果无法完成推导，并且又没有传递具体的类型，默认为空对象

泛型可以设置默认值

```ts
interface hasNameProperty {
    name: string
}
//对T的限制，也就是T中必须有name属性
function fn<T extends hasNameProperty>(obj: T): T {
    return obj;
}

const o = {
    name:"buouyu",
    age:20
}
const oo = {
    age:20
}

const newO = fn(o);
// const newOo = fn(oo) //报错
```
**两个变量**
```ts
function fn<T, K>(arr1: T[], arr2: K[]): (T | K)[] {
    return arr1.length>arr2.length?arr1:arr2;
}

const result = fn([1, 3, 4], ["a", "b", "c"]);
```
**在类中的使用**
```ts
export class ArrayHelper<T> {
    constructor(private arr: T[]) { }
    take(): T[] {
        const newArr: T[] = [];
        newArr.push(this.arr[0])
        return newArr;
    }
}
```
