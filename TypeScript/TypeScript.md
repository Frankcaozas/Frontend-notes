## 1.begin

### generate config file
```bash
tsc --init
```

### some configs
```json
/* Language and Environment */

"target": "es2016",      
/* Set the JavaScript language version for emitted JavaScript and include compatible library 

/* Modules */
    "module": "commonjs",                                
/* Specify what module code is generated. */
     "rootDir": "./src",                                  
/* Specify the root folder within your source files. */

 "outDir": "./dist",                                   
/* Specify an output folder for all emitted files. */
 "removeComments": true,                           
/* Disable emitting comments. */

```

### compile ts file
```bash
tsc index.ts(filename)
```

### debug ts file with node

add this line in launch.json
```json
"preLaunchTask": "tsc build - tscongig.json",
```
## 2.types
### any
```typescript
let levelL = "typescript"//编译器可自动推断出类型
levelL = 123 

function render(document:any) {
	//不写any报错
	//或者将tsconfig中 TypeCheking: noImplicitAny 改为false
	console.log(document)
}
```
### Uknown
```typescript
let userinput: unknown
let username: string
userinput = 5 
userinput = 'frankcao'

//报错因为 userinput类型为 unknown， 不知道是string
//username = userinput

if(typeof userinput === 'string'){
  username = userinput
}
```
### Arrays
```typescript
let numbers: number[] = [1, 2, 3]

```

### Tuple 
固定长度数组，常用于处理一对值
```typescript
let user: [number, string] = [1, 'frankcao']
user.push()//不会报错，待修复

```

### Enums
```typescript
enum Size{small， medium, large} //默认从0开始
enum Size{small=1, medium, large}
//声明为const会优化生成的js代码
const enum Size{small='a', medium='b', large='c'}

```

### Union
管道符 : |
```typescript
//input可为 string 也可 number
const combine = (input1: number | string, input2: number | string ):number | string => {
  let result 
  if(typeof input1 === 'number' && typeof input2 === 'number'){
    result = input1 + input2
  }else{
    result = input1.toString() + input2.toString()
  }
  return result
}

console.log(combine(21,'21'));
console.log(combine(21,21));
console.log(combine('max','21'));

```
### Literal
```typescript
let literal: 'as-number' | 'as-string'
```
### Functions
可选参数

1. 用？和  ||
```typescript
function calculate1(income:number, tax?:number
// ? 可选参数
): number{
	if((income || 0.2) < 1000){
		return income
	}	
	return 0
}



calculate(1000)
calculate(1000,0.2)

function add(n1:number, n2: number):any
```
2.给定初始值
```typescript
function calculate1(income:number, tax=0.2): number{
	if((income || 0.2) < 1000){
		return income
	}	
	return 0
}

// let fun: (a:number, b:number)=>number 
let fun: Function
fun = combine
```
#### never
函数可以返回的类型
产生异常，永远不会返回任何值
而void编译为js后 会返回 undefined
```typescript
function generateError(msg: string, code: number):never{
  throw {message: msg, errorCode: code}
}

const result = generateError('an erroe occuored', 500)
console.log(result);
```
### 

### Object
```typescript
let employee = {id:1}
employee.name = 'mosh' //不能随意加字段


```
### Type Aliases 别名
```typescript
type NumberOrString = number | string 
```
## 3. compiler(and its configuration)
#### watch mode
```bash
//单个文件
tsc filename -w 
tsc filename --watch

//初始化后运行tsc 可编译所有ts文件
tsc --init

tsc
tsc -w
tsc --watch
```

#### include exclude
```json
"exclude": [
    "node_modules", // default
  ],
  "include": [
    //可指定文件夹和文件
  ],
  "files": [
    "app.ts"
    //不可指定文件夹
  ]
```
```typescript
{
  "compilerOptions": {
    /* Visit https://aka.ms/tsconfig to read more about this file */
    /* Projects */
    // "incremental": true,                              /* Save .tsbuildinfo files to allow for incremental compilation of projects. */
    // "composite": true,                                /* Enable constraints that allow a TypeScript project to be used with project references. */
    // "tsBuildInfoFile": "./.tsbuildinfo",              /* Specify the path to .tsbuildinfo incremental compilation file. */
    // "disableSourceOfProjectReferenceRedirect": true,  /* Disable preferring source files instead of declaration files when referencing composite projects. */
    // "disableSolutionSearching": true,                 /* Opt a project out of multi-project reference checking when editing. */
    // "disableReferencedProjectLoad": true,             /* Reduce the number of projects loaded automatically by TypeScript. */
    /* Language and Environment */
    "target": "es2016", /* Set the JavaScript language version for emitted JavaScript and include compatible library declarations. */
    // "lib": [],                                        /* Specify a set of bundled library declaration files that describe the target runtime environment. */
    // "jsx": "preserve",                                /* Specify what JSX code is generated. */
    // "experimentalDecorators": true,                   /* Enable experimental support for TC39 stage 2 draft decorators. */
    // "emitDecoratorMetadata": true,                    /* Emit design-type metadata for decorated declarations in source files. */
    // "jsxFactory": "",                                 /* Specify the JSX factory function used when targeting React JSX emit, e.g. 'React.createElement' or 'h'. */
    // "jsxFragmentFactory": "",                         /* Specify the JSX Fragment reference used for fragments when targeting React JSX emit e.g. 'React.Fragment' or 'Fragment'. */
    // "jsxImportSource": "",                            /* Specify module specifier used to import the JSX factory functions when using 'jsx: react-jsx*'. */
    // "reactNamespace": "",                             /* Specify the object invoked for 'createElement'. This only applies when targeting 'react' JSX emit. */
    // "noLib": true,                                    /* Disable including any library files, including the default lib.d.ts. */
    // "useDefineForClassFields": true,                  /* Emit ECMAScript-standard-compliant class fields. */
    // "moduleDetection": "auto",                        /* Control what method is used to detect module-format JS files. */
    /* Modules */
    "module": "ES2020", /* Specify what module code is generated. */
    // "rootDir": "./",                                  /* Specify the root folder within your source files. */
    // "moduleResolution": "node",                       /* Specify how TypeScript looks up a file from a given module specifier. */
    // "baseUrl": "./",                                  /* Specify the base directory to resolve non-relative module names. */
    // "paths": {},                                      /* Specify a set of entries that re-map imports to additional lookup locations. */
    // "rootDirs": [],                                   /* Allow multiple folders to be treated as one when resolving modules. */
    // "typeRoots": [],                                  /* Specify multiple folders that act like './node_modules/@types'. */
    // "types": [],                                      /* Specify type package names to be included without being referenced in a source file. */
    // "allowUmdGlobalAccess": true,                     /* Allow accessing UMD globals from modules. */
    // "moduleSuffixes": [],                             /* List of file name suffixes to search when resolving a module. */
    // "resolveJsonModule": true,                        /* Enable importing .json files. */
    // "noResolve": true,                                /* Disallow 'import's, 'require's or '<reference>'s from expanding the number of files TypeScript should add to a project. */
    /* JavaScript Support */
    // "allowJs": true,                                  /* Allow JavaScript files to be a part of your program. Use the 'checkJS' option to get errors from these files. */
    // "checkJs": true,                                  /* Enable error reporting in type-checked JavaScript files. */
    // "maxNodeModuleJsDepth": 1,                        /* Specify the maximum folder depth used for checking JavaScript files from 'node_modules'. Only applicable with 'allowJs'. */
    /* Emit */
    // "declaration": true,                              /* Generate .d.ts files from TypeScript and JavaScript files in your project. */
    // "declarationMap": true,                           /* Create sourcemaps for d.ts files. */
    // "emitDeclarationOnly": true,                      /* Only output d.ts files and not JavaScript files. */
    // "sourceMap": true,                                /* Create source map files for emitted JavaScript files. */
    // "outFile": "./",                                  /* Specify a file that bundles all outputs into one JavaScript file. If 'declaration' is true, also designates a file that bundles all .d.ts output. */
    // "outDir": "./",                                   /* Specify an output folder for all emitted files. */
    // "removeComments": true,                           /* Disable emitting comments. */
    // "noEmit": true,                                   /* Disable emitting files from a compilation. */
    // "importHelpers": true,                            /* Allow importing helper functions from tslib once per project, instead of including them per-file. */
    // "importsNotUsedAsValues": "remove",               /* Specify emit/checking behavior for imports that are only used for types. */
    // "downlevelIteration": true,                       /* Emit more compliant, but verbose and less performant JavaScript for iteration. */
    // "sourceRoot": "",                                 /* Specify the root path for debuggers to find the reference source code. */
    // "mapRoot": "",                                    /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,                          /* Include sourcemap files inside the emitted JavaScript. */
    // "inlineSources": true,                            /* Include source code in the sourcemaps inside the emitted JavaScript. */
    // "emitBOM": true,                                  /* Emit a UTF-8 Byte Order Mark (BOM) in the beginning of output files. */
    // "newLine": "crlf",                                /* Set the newline character for emitting files. */
    // "stripInternal": true,                            /* Disable emitting declarations that have '@internal' in their JSDoc comments. */
    // "noEmitHelpers": true,                            /* Disable generating custom helper functions like '__extends' in compiled output. */
    // "noEmitOnError": true,                            /* Disable emitting files if any type checking errors are reported. */
    // "preserveConstEnums": true,                       /* Disable erasing 'const enum' declarations in generated code. */
    // "declarationDir": "./",                           /* Specify the output directory for generated declaration files. */
    // "preserveValueImports": true,                     /* Preserve unused imported values in the JavaScript output that would otherwise be removed. */
    /* Interop Constraints */
    // "isolatedModules": true,                          /* Ensure that each file can be safely transpiled without relying on other imports. */
    // "allowSyntheticDefaultImports": true,             /* Allow 'import x from y' when a module doesn't have a default export. */
    "esModuleInterop": true, /* Emit additional JavaScript to ease support for importing CommonJS modules. This enables 'allowSyntheticDefaultImports' for type compatibility. */
    // "preserveSymlinks": true,                         /* Disable resolving symlinks to their realpath. This correlates to the same flag in node. */
    "forceConsistentCasingInFileNames": true, /* Ensure that casing is correct in imports. */
    /* Type Checking */
    "strict": true, /* Enable all strict type-checking options. */
    // "noImplicitAny": true,                            /* Enable error reporting for expressions and declarations with an implied 'any' type. */
    // "strictNullChecks": true,                         /* When type checking, take into account 'null' and 'undefined'. */
    // "strictFunctionTypes": true,                      /* When assigning functions, check to ensure parameters and the return values are subtype-compatible. */
    // "strictBindCallApply": true,                      /* Check that the arguments for 'bind', 'call', and 'apply' methods match the original function. */
    // "strictPropertyInitialization": true,             /* Check for class properties that are declared but not set in the constructor. */
    // "noImplicitThis": true,                           /* Enable error reporting when 'this' is given the type 'any'. */
    // "useUnknownInCatchVariables": true,               /* Default catch clause variables as 'unknown' instead of 'any'. */
    // "alwaysStrict": true,                             /* Ensure 'use strict' is always emitted. */
    // "noUnusedLocals": true,                           /* Enable error reporting when local variables aren't read. */
    "noUnusedParameters": true, /* Raise an error when a function parameter isn't read. */
    // "exactOptionalPropertyTypes": true,               /* Interpret optional property types as written, rather than adding 'undefined'. */
    // "noImplicitReturns": true,                        /* Enable error reporting for codepaths that do not explicitly return in a function. */
    // "noFallthroughCasesInSwitch": true,               /* Enable error reporting for fallthrough cases in switch statements. */
    // "noUncheckedIndexedAccess": true,                 /* Add 'undefined' to a type when accessed using an index. */
    // "noImplicitOverride": true,                       /* Ensure overriding members in derived classes are marked with an override modifier. */
    // "noPropertyAccessFromIndexSignature": true,       /* Enforces using indexed accessors for keys declared using an indexed type. */
    // "allowUnusedLabels": true,                        /* Disable error reporting for unused labels. */
    // "allowUnreachableCode": true,                     /* Disable error reporting for unreachable code. */
    /* Completeness */
    // "skipDefaultLibCheck": true,                      /* Skip type checking .d.ts files that are included with TypeScript. */
    "skipLibCheck": true /* Skip type checking all .d.ts files. */
  },
  // "exclude": [
  //   "node_modules", // default
  // ],
  // "include": [
  //   //可指定文件夹和文件
  // ],
  // "files": [
  //   "app.ts"
  //   //不可指定文件夹
  // ]
}
```
```typescript
//开启strictNullChecks
// const button = document.querySelector('button')！
const button = document.querySelector('button')
button?.addEventListener('click', ()=>{
  console.log('click')
})
```
#### debug with vscode 
开启sorceMap
下载debug chrome插件
## 4.TypeScript项目
### 声明空间
#### 类型声明空间
包含用来做类型注解的内容
```typescript
class Foo {}
interface Bar {}
type Bas = {};

let foo: Foo;
let bar: Bar;
let bas: Bas;
```
interface 不能用来做变量, 没有声明在变量空间中
```javascript
interface Bar {}
const bar = Bar; // Error: "cannot find name 'Bar'"
```

#### 变量声明空间
```typescript
class Foo {}
const someVar = Foo;
const someOtherVar = 123;
```
### 模块
#### 全局模块
在默认情况下，当你开始在一个新的 TypeScript 文件中写下代码时，它处于全局命名空间中。如在 foo.ts 里的以下代码。
```typescript
const foo = 123;
const bar = foo; // allowed
```
如果你在相同的项目里创建了一个新的文件 bar.ts，TypeScript 类型系统将会允许你使用变量 foo，就好像它在全局可用一样：
毋庸置疑，使用全局变量空间是危险的，因为它会与文件内的代码命名冲突。我们推荐使用下文中将要提到的文件模块。
#### 文件模块
ES 模块语法
## 5.Classes & Interfaces
### this作为参数
```typescript
class Department {
  name: string
  constructor(name: string) {
    this.name = name
  }
  describe(this: Department) {
    //指定this是Department的实例
    console.log('Department: ' + this.name)
  }
}

const accouting = new Department('Accounting')
accouting.describe()//Department: Accounting

const accoutingCopy = { describe: accouting.describe }
// accoutingCopy.describe()
//报错，因为accoutingCopy不是Department的实例
//加上name不报错

```
### private /public
```typescript
class Department {
  name: string
  private employees: string[] = []
  constructor(name: string) {
    this.name = name
  }
  
  addEmployee(employee: string){
    this.employees.push(employee)
  }
}
```
### shothand initialization
```typescript
class Department {
  // id: string
  // name: string
  private employees: string[] = []
  //简化构造器写法
  constructor(public name: string, public id: string) {

  }

  describe(this: Department) {
    console.log(`Department: ${this.id}` + this.name)
  }
}
```
### readonly
只读不可改
初始化时定义
```typescript
class Department {
  // id: string
  // name: string
  private employees: string[] = []
  //简化构造器写法
  constructor(public name: string, public readonly id: string) {

  }
  addEmployee(employee: string){
    this.id = 'd2'//报错
    this.employees.push(employee)
  }
}
```
### Inheritance
```typescript
class ITDepartment extends Department {
  constructor(id: string, private admins: string[]) {
    super('IT', id)
  }
}
const IT = new ITDepartment('d1',['MAX'])
console.log(IT)
```
### Override properties
private 子类无法访问
protected 子类可以
```typescript
class Department {
  // id: string
  // name: string
  protected employees: string[] = []
  //简化构造器写法
  constructor(public name: string, public readonly id: string) {}

  describe(this: Department) {
    //指定this是Department的实例
    console.log(`Department: ${this.id}` + this.name)
  }
  addEmployee(employee: string) {
    this.employees.push(employee)
  }
}

class AccountingDepartment extends Department {
  constructor(id: string, private reports: string[]){
    super('Accounting', id)
  }

  //override
  addEmployee(employee: string): void {
    if(this.employees.indexOf(employee) === -1){
      return
    }
    this.employees.push(employee)
  }
}
```
### get/set
get 不加()
set加
```typescript
class AccountingDepartment extends Department {
  private lastReport: string
  constructor(id: string, private reports: string[]){
    super('Accounting', id)
    this.lastReport = reports[0]
  }

  //override
  addEmployee(employee: string): void {
    if(this.employees.indexOf(employee) === -1){
      return
    }
    this.employees.push(employee)
  }

  get mostRecentReport(){
    if(this.lastReport) 
      return this.lastReport
    throw new Error('no report found')
  }
}

const account = new AccountingDepartment('a1', ['zzz'])

account.mostRecentReport //getter
account.mostRecentReport()//setter
```
### stactic
### abstract classes
用于类的某些方法必须实现，而具体实现取决于具体情况时
类上加abstract
方法前加abstract
**不能实例化**
```typescript
abstract class Department {
  protected employees: string[] = []
  //简化构造器写法
  constructor(public name: string, public readonly id: string) {}

  abstract describe(this: Department): void

  addEmployee(employee: string) {
    this.employees.push(employee)
  }
}
```
### Singletons & private Constructors
```typescript
class AccountingDepartment extends Department {
  private static instance: AccountingDepartment
 
  private lastReport: string
  private constructor(id: string, private reports: string[]) {
    super('Accounting', id)
    this.lastReport = reports[0]
  }
  
  static getInstance() {
    if (AccountingDepartment.instance) {
      return this.instance
    }
    this.instance = new AccountingDepartment('a1', ['Max'])
    return this.instance
  }
}
```
## 6.Interface
和 custom types 的区别：
1.自定义类型更灵活，比如union(联合类型）
2.interface更清晰，只能用于定义类的结构
和 abstract classes的区别
1.abstract classes可以有具体的实现
2.interface只能定义结构， 不能有具体实现
3.只能继承一个类， 但可以实现多接口
#### readonly  interface properties
属性可以为readonly 但不能为private public
```typescript
interface Person {
  readonly name: string
  //private name: string
  age: number
}
```
#### extending interfaces
接口可继承多个接口
#### interfaces as function types
```typescript
// type AddFn = (a: number, b: number)=> number
interface AddFn{
  (a: number, b: number): number
}
let add:AddFn
```
#### compiling interfaces to javascript
interface 只在编译时生效
运行时无效
编译为js后消失（no output）

## 7.Advanced Types

### Intersection

用&表示交集类型
   | 表示联合

```ts
type Admin = {

name: string,

privileges: string[]

}

type Emp = {

name: string,

startDate: Date

}
type ElevatedEmp = Admin & Emp
```

### Type Guards 

使用联合类型时，需要知道具体使用的是哪种类型

```ts
1.基本类型， *typeof*
2.对象， *instanceof*  or *in*

注意！
对 interface 不能用 instance 
因为 interface 不存在于 runtime
```

### Discriminated Types

discriminated 识别,辨别
可用于interface
```ts
interface Bird {
  type: 'bird'
  flySpeed: number
}

interface Horse{
  type: 'horse'
  runningSpeed: number
}

type Animalv = Bird | Horse
function moveAnimal(animal: Animalv) {
  let speed
  switch (animal.type) {
    case 'bird':
      break;
    case 'horse':
      break
    default:
      break;
  }
}
```

### Type Cast

两种写法


```ts
//HTMLElement 没有input对应的属性
const inputElement = document.querySelector('#id')!
//1
const inputElement = <HTMLInputElement>document.querySelector('#id')!

	  
//2
const inputElement = document.querySelector('#id')! as
HTMLInputElement
inputElement.value = 'hi'
```

 ### Index Types

不知道属性的数量和名称
```ts
interface ErrorContainer {
  [prop: string]: string
}

const error: ErrorContainer = {
  email: 'zzz',
  username: 'ssss'
}
```
更多用法：https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html

### Function Overload

```ts
 function add(a: string, b: string): string

function add(a: number, b: number): number

function add(a: Combineable, b: Combineable) {

if(typeof a === 'string' || typeof b ==='string'){

return a.toString() + b.toString()

}

return a+b

}

const res = add(1, 2)
//编译器自动判断出res的类型
```
### Nullish Coalescing
a ?? b
如果a为 null 或 undefined 
值为b

## 8.Generics

### built in generics
```ts
const array: Array<number> = []

const promise= new Promise<string>((resolve) => {
  resolve('s s ss s')
})

promise.then((data) => {
  const a = data.split(' ')
})
```

### generic function
```ts
function merge<T extends {}, U>(objA: T, objB: U) {
  return Object.assign(objA, objB)
}

const mergeObj = merge(
  {
    name: 'frank',
  },
  {
    id: 1
  }
)
merge<string, number>('aaa',123)
```

### constrains
(泛型约束)
1.extends

```ts
function merge<T extends object, U extends object>(objA: T, objB: U) {
  
}

```

2.keyof
```ts
function extractAndConvert<T extends object,U extends keyof T>(obj: T, key: U ){
  return obj[key]
}  
```

## 9.Decorator

#### tsconfig要求
```json
"target": "es2016",
"noUnusedParameters": true
```

```ts
function Logger(target: Function){
  console.log(target)
}

@Logger
class Person{
  constructor(public name: string) {
    console.log('creating preson' + this.name)
  }
}
```
decorator在类定义时执行，而不是在类实例化时

#### Decorator Factory(如何传参)

修饰类
```ts
const WithTemplate = (logString: string)=>{
  return function (target: object) {
    console.log(logString)
    console.log(target)
    //this is the template pass in
	//[class Person]
  }
}

@WithTemplate('this is the template pass in')
class Person{
  constructor(public name: string) {
    console.log('creating preson' + this.name)
  }
}
```

#### Multiple Decorators

*可以添加多个装饰器* 
执行顺序：
	工厂函数自下而上
	外部的自上而下

```ts
function Logger(str: string){
  console.log('logger factory')
  return (constructor: Function )=>{
    console.log(constructor + str)
  }

}

const WithTemplate = (logString: string)=>{
  console.log('WithTemplate factory')
  return function (target: object) {
    console.log(logString)
    console.log(target)
  }
}
@Logger('logger str')
@WithTemplate('this is the template pass in')
class Person{
  constructor(public name: string) {
    console.log('creating preson' + this.name)
  }
}

结果
1.logger factory
2.WithTemplate factory
3.this is the template pass in
[class Person]
4. constructor + logger str 
```

#### 修饰符位置
https://www.typescriptlang.org/docs/handbook/decorators.html#decorator-composition

#### Decorator return Type
##### 1.return 一个新的类 
*加在类上时*
覆盖原有的constructor， 可以在new 这个类时执行相应的逻辑
```ts
function WithTemplate(template: string, hookId: string) {
  console.log('TEMPLATE FACTORY');
  return function<T extends { new (...args: any[]): {name: string} }>(
    originalConstructor: T
  ) {
    return class extends originalConstructor {
      constructor(..._: any[]) {
        super();
        console.log('Rendering template');
        const hookEl = document.getElementById(hookId);
        if (hookEl) {
          hookEl.innerHTML = template;
          hookEl.querySelector('h1')!.textContent = this.name;
        }
      }
    };
  };
}
```

##### 2. Return Descriptor
method or accessor
加在方法或者是getter setter上 返回一个新的descriptor

```ts
function AutoBind(_1: any, _2: string, descriptor: PropertyDescriptor):PropertyDescriptor{
  const originFunc = descriptor.value
  const newDescriptor: PropertyDescriptor = {
    configurable: true,
    enumerable: false,
    
    在新的getter中绑定this
    get(){
      return originFunc.bind(this)
    }
  }
  return newDescriptor
}
class Printer {
  message =  'This Works!';
  
  @AutoBind
  showMessage (){
    console.log(this.message)
  }
}

const printer = new Printer()
const buttona = document.querySelector('button')!
buttona.addEventListener('click', printer.showMessage)
```

#### 实现Validator

```ts

interface ValidatorConfig {
  [property: string]: {
    [validatableProp: string]: string[]; // ['required', 'positive']
  };
}

const registeredValidators: ValidatorConfig = {};

function Required(target: any, name: string){
  registeredValidators[target.constructor.name] ={
    ...registeredValidators[target.constructor.name],
     [name]: ['require']
  }
}

function PositiveNumber(target: any, name: string){
  registeredValidators[target.constructor.name] ={
    ...registeredValidators[target.constructor.name],
     [name]: ['positive']
  }
}

const validate = (obj: any):boolean =>{
  const config = registeredValidators[obj.constructor.name]
  for(const validator in config){
    for(const validatorName of config[validator]){
      switch (validatorName) {
        case 'require':
          console.log('require')
          if(!obj[validator]){
            return false
          }
          break;
        case 'positive':
          console.log('positive')
          if(obj[validator]<=0){
            return false
          }
          break
        default:
          break;
      }
    }
  }
  return true
}

class Course {
  @Required
  title: string;
  @PositiveNumber
  price: number;

  constructor(t: string, p: number) {
    this.title = t;
    this.price = p;
  }
}

const courseForm = document.querySelector('form')!;
courseForm.addEventListener('submit', event => {
  event.preventDefault();
  const titleEl = document.getElementById('title') as HTMLInputElement;
  const priceEl = document.getElementById('price') as HTMLInputElement;

  const title = titleEl.value;
  const price = +priceEl.value;

  const createdCourse = new Course(title, price);

  if (!validate(createdCourse)) {
    alert('Invalid input, please try again!');
    return;
  }
  console.log(createdCourse);
})
```

###