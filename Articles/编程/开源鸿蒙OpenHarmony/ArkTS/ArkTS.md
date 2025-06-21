# 简单介绍

- ArkTS围绕应用开发在TypeScript（简称TS）生态基础上做了进一步扩展，继承了TS的所有特性，是TS的超集。
- ArkTs的语法与C/C++、Python等语言有相同和相似的地方，比如if else、for循环等完全相同的语法以下不会写# 基础输出语句

- 使用```console
```
类中的```log(内容)
```
方法
- 内容必须以字符串开头，以 ```,
```
 间隔
```arkts
console.log("Hello world");
```
# 基础数据存储

- 三种基本数据类型
- ```string
```
 字符串
- ```number
```
 数字（包括 正 负 小数）
- ```boolean
```
 布尔
- 两种基础关键字
- 常量关键字 ```const
```
，值不可更改
- 变量关键字 ```let
```
，值可更改
- 使用方法
- ```关键字 变量命 : 变量类型 = 数据值
```

- 关键字一定要加
- const 变量一定要给初始值
```arkts
let num ：number = 0
```
# 数组

- 格式：```关键字 变量命 : 变量类型[] = [数据值1, 数据值2 ,数据值3]
```

```arkts
let name:string[] = ['张三','李四','王五','赵六']
console.log(name[1])
```
# 函数

- ```function 函数名 ( 变量名 : 变量类型){ }
```

```arkts
const a:number = 111
const b:number = 222

function add(a:number,b:number){
  
  let ab:number = a+b
  return ab
  
}

const ab:number = add(a,b)
console.log("a+b=",ab)
```
# 对象

- ArkTS的对象不是类，更像是能封装函数的结构体
- 关键字：```interface
```
## 对象-属性

- 属性的创建和结构体基本相同
```arkts
interface Stu{
  name:string	
  age:number
  score:number
}

let ZhangSan:Stu = {
  name: "张三" ,
  age: 18,
  score: 90
}

console.log("name =",ZhangSan.name)
console.log("age =",ZhangSan.age)
console.log("score =",ZhangSan.score)
```
## 对象-方法

- 方法的创建和函数的创建基本相同
- 无输入方法使用时一定要跟括号，不然不会被调用
```arkts
.interface Maths{
  a:number
  b:number
  ab:number
  Add:(a:number,b:number) => number
  Output:() => void
}

let my_maths: Maths ={
  a:111,
  b:222,
  ab:0,
  Add:(a:number,b:number)=>{
    let num:number = a+b
    return num
  },
  Output:()=>{
    console.log("Output")
  }
}

my_maths.Output()
console.log("a =",my_maths.a)
console.log("b =",my_maths.b)
my_maths.ab = my_maths.Add(my_maths.a,my_maths.b)
console.log("ab =",my_maths.ab)
```
​
