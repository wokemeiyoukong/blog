#### 原始数据类型

+ `布尔值、数值、字符串、null、undefined、Symbol` 

  + 布尔值

    ```typescript
    // boolean: 定义布尔值类型 
    let isDone: boolean = fasle;
    // 使用构造函数Boolean 创造的对象不是布尔值 返回的是布尔对象
    let createdByNewBoolean: boolean = new Boolean(1);
    
    // Type 'Boolean' is not assignable to type 'boolean'.
    //   'boolean' is a primitive, but 'Boolean' is a wrapper object. Prefer using 'boolean' when possible.
    ```

  + 数值

    ```typescript
    let numberone: number = 6
    // 0x 表示16进制
    let numbertwo: number = 0xf00d
    // 0b 表示二进制
    let numberthree: number = 0b1111
    // 0o 表示8进制
    let numberfour: number = 0o744
    // NaN/undefined 为所有类型的子类型
    let numberfive: number = NaN
    let numbersix: number = Infinity
    console.log(numbertwo,numberthree,numberfour,numberfive,numbersix) // 61453 15 484 NaN Infinity
    ```

  + 字符串

    ```typescript
    let myName: string = 'Tom';
    let myAge: number = 25;
    // 模板字符串
    let sentence: string = `Hello, my name is ${myName}.
    I'll be ${myAge + 1} years old next month.`;
    ```

  + `Null` 和 `Undefined` 

+ 是所有类型的子类型 

+ `Void` 空值

  + 可以表示没有任何返回值的函数

    ```typescript
    function voidfunc (): void {
      console.log('没有返回值')
    }
    ```

#### 任意值

+ 任意值（Any）用来表示允许赋值为任意类型

  + 未指定的类型会被识别为任意值类型

  ```typescript
  let variabletype: any
  ```

#### `never` 

+ 是任何类型的子类型，通常用来表示抛出异常或无法执行到终点（死循环）。
+ 永不存在的值 抛出异常 没有返回值的函数表达式 

#### `Unknown` 类型

+ 与 `any` 顶级类型 赋值都是类型正确



#### 类型推论

+ 没有明确指定类型，会根据规则推断一个类型

  ```typescript
  let variabletype = 'name'
  variabletype = 7
  // 报错 ts会推论 variabletype: string 
  // 未指定时推断成 any 类型
  let myFavoriteNumber;
  myFavoriteNumber = 'seven';
  myFavoriteNumber = 7;
  ```

#### 联合类型

+ 表示取值可以为多类型中的一种

  ```typescript
  let myFavoriteNumber: string | number;
  myFavoriteNumber = 'seven';
  myFavoriteNumber = 7;
  // 不确定类型时只能访问共有的属性和方法
  // 联合类型会在赋值时根据类型推论推断出一个类型
  function getLength(something: string | number): number {
      return something.length;
  }
  // index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
  //   Property 'length' does not exist on type 'number'.
  ```

#### 交叉类型

+ 将多个类型合并为一个类型 包含多个类型的所有特性

  ```ts
  type Person = { x: number }
  type Normal = Person & { y: number }
  ```

+ 同名的交叉类型，基本类型会转变成 `never` 类型，非基本类型可以合并

  ```ts
  interface X { c: string, d: string }
  interface Y { c: number, e: string }
  
  type XY = X & Y
  
  let p: XY
  p = { c: 6, d: 'x', e: 'x'} // 此时 c的类型 变成 sting&number -> never 类型
  
  interface D { d: boolean }
  interface E { e: string }
  interface F { f: number }
  
  interface A { x: D }
  interface B { x: E }
  interface C { x: F }
  
  const abc: ABC = {
      x: {
          d: true,
          e: 'x',
          f: 4
      }
  } // 类型合并
  ```

  

#### 接口

+ `(Interfaces)` 定义对象的类型/类的一部分行为进行抽象

  + 接口中定义的属性必须在实例化中一一对应

  ```typescript
  interface Person {
      name: string,
      age: number
    }
    
    interface Person {
      name: string,
          // ？表示可选参数 赋值时是否定义都可以
      age?:number
    }
      interface Person {
          name: string,
          age: number,
              // 任意属性
          [propName: string]:any
        }
    
      interface Person {
          // 只读属性 只能在创建时被赋值
          readonly id:number
          name: string,
              // ？表示可选参数 赋值时是否定义都可以
              age?:number
    }
    let person: Person = {
      name: 'tom',
      age: 23,
        // gender属性未在接口Person中定义 ts中会报错
      gender: male
    }
  ```

+ 函数类型接口

  ```typescript
  // 约束函数的参数和返回值类型 
  interface discont {
    // (price: number) 函数签名 限制参数的类型
    // 返回值也为 number 类型
    (price: number) : number
  }
  
  // 使用类型别名
  type Add = (x: number, y: number) => number
  const add: Add = (a:number, b:number) => a+b
  ```

+ 可索引类型接口

  ```typescript
  
  interface stringArray {
   // 索引为number类型 值为string类型 
    [index: number]: string
  }
  
  interface stringObject {
    // 键为string类型 值为string类型
    [key: string]: string
  }
  ```

+ 混合类型接口

  ```typescript
  // 
  interface Counter {
      (start: number): string;
      interval: number;
      reset(): void;
  }
  
  function getCounter(): Counter {
      // <Counter> 创建一个对象counter并指定泛型
      let counter = <Counter>function (start: number) { };
      counter.interval = 123;
      counter.reset = function () { };
      return counter;
  }
  
  let c = getCounter();
  c(10);
  c.reset();
  c.interval = 5.0;
  ```

  

#### 数组的类型

+ 「类型 + 方括号」表示法

  ```typescript
  // 不能讲string类型赋值给 number[] 声明的数组
  let arraynumber: number[] = [1,3,4,5,'q']
  // 数组的一些方法的参数也会根据数组在定义时约定的类型进行限制：
  let fibonacci: number[] = [1, 1, 2, 3, 5];
  fibonacci.push('8');
  // Argument of type '"8"' is not assignable to parameter of type 'number'.
  ```

+ 数组泛型

  ```typescript
  let fibonacci: Array<number> = [1, 1, 2, 3, 5];
  ```

+ 用接口表示数组

  ```typescript
  // 常用来表示类数组。
  interface NumberArray {
      [index: number]: number;
  }
  let fibonacci: NumberArray = [1, 1, 2, 3, 5];
  // 
  function sum() {
      let args: {
          [index: number]: number;
          length: number;
          callee: Function;
      } = arguments;
  }
  ```

+ `any` 在数组中的应用

  ```typescript
  // 数组中允许出现任意类型
  let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }];
  ```

#### 函数的类型

+ 函数声明

  ```typescript
  // 函数声明（Function Declaration）
  function sum(x: string = 'tom', y?: number): string {
      return x + y;
  }
  // ?表示可选参数，可选参数后面不能再接必需参数
  // =表示该参数的默认参数 默认参数识别为可选参数
  ```

+ 函数表达式

  ```typescript
  // 函数表达式（Function Expression）
  let mySum:(x: string,y: number) => string = function (x: string, y: number): string {
      return x + y;
  };
  ```

+ 接口定义函数

  ```typescript
  interface SearchFunc {
      (source: string, subString: string): boolean;
  }
  
  let mySearch: SearchFunc;
  mySearch = function(source: string, subString: string) {
      return source.search(subString) !== -1;
  }
  ```

+ 剩余参数

  ```typescript
  function push(array: any[], ...items: any[]) {
      items.forEach(function(item) {
          array.push(item);
      });
  }
  
  let a = [];
  push(a, 1, 2, 3);
  ```

+ 重载

  + 重载允许一个函数接受不同数量或类型的参数时，作出不同的处理

  ```typescript
  function reverse(x: number): number;
  function reverse(x: string): string;
  function reverse(x: number | string): number | string {
      if (typeof x === 'number') {
          return Number(x.toString().split('').reverse().join(''));
      } else if (typeof x === 'string') {
          return x.split('').reverse().join('');
      }
  }
  ```

#### 类型断言

+ 类型断言（Type Assertion）可以用来手动指定一个值的类型。

  ```typescript
  // <类型>值  
  // 值 as 类型(在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种)
  
  function getLength(something: string | number): number {
      // 将 something 的类型断言成 string
      if ((<string>something).length) {
          return (<string>something).length;
      } else {
          return something.toString().length;
      }
  }
  // 类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的：
  ```

#### 类型别名

+ 类型起新名字

  ```typescript
  type Name = string;
  type NameResolver = () => string;
  type NameOrResolver = Name | NameResolver;
  function getName(n: NameOrResolver): Name {
      if (typeof n === 'string') {
          return n;
      } else {
          return n();
      }
  }
  // type 创建类型别名
  ```

#### 字符串字面量

+ 字符串字面量类型用来约束取值只能是某几个字符串中的一个。

  ```typescript
  type EventNames = 'click' | 'scroll' | 'mousemove';
  function handleEvent(ele: Element, event: EventNames) {
      // do something
  }
  
  handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
  handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 不能为 'dbclick'
  ```

#### 元祖

+ 数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

  ```typescript
  let tom: [string, number] = ['Tom', 25];
  // 但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。
  
  let tom: [string, number];
  tom = ['Tom', 25];
  tom.push('male');
  tom.push('female');
  console.log(tom) //  ["Tom", 25, "male", "female"]
  ```

#### 枚举

+ 枚举（`Enum`）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。

  ```typescript
  enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
  
  enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
  
  console.log(Days["Sun"] === 0); // true
  console.log(Days["Mon"] === 1); // true
  console.log(Days["Tue"] === 2); // true
  console.log(Days["Sat"] === 6); // true
  
  console.log(Days[0] === "Sun"); // true
  console.log(Days[1] === "Mon"); // true
  console.log(Days[2] === "Tue"); // true
  console.log(Days[6] === "Sat"); // true
  
  // 字符串枚举
  // 异构枚举
  // 反向映射
  ```

#### 类

+ 类(Class)：定义了一件事物的抽象特点，包含它的属性和方法

+ 对象（Object）：类的实例，通过 `new` 生成

+ 面向对象（`oop`）的三大特性：封装、继承、多态

+ 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据

+ 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性

+ 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `Cat` 和 `Dog` 都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`

+ 存取器（getter & setter）：用以改变属性的读取和赋值行为

+ 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 `public` 表示公有属性或方法

+ 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现

+ 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

+ 属性和方法

  ```typescript
  // class 定义类
  class Animal {
      // constructor 定义构造函数
      constructor(name) {
          this.name = name;
      }
      sayHi() {
          return `My name is ${this.name}`;
      }
  }
  
  let a = new Animal('Jack');
  console.log(a.sayHi()); // My name is Jack
  ```

+ 类的继承

  ```typescript
  class Cat extends Animal {
      constructor(name) {
          super(name); // 调用父类的 constructor(name)
          console.log(this.name);
      }
      sayHi() {
          return 'Meow, ' + super.sayHi(); // 调用父类的 sayHi()
      }
  }
  
  let c = new Cat('Tom'); // Tom
  console.log(c.sayHi()); // Meow, My name is Tom
  ```

+ 存取器

  ```typescript
  class Animal {
      constructor(name) {
          this.name = name;
      }
      get name() {
          return 'Jack';
      }
      set name(value) {
          console.log('setter: ' + value);
      }
  }
  
  let a = new Animal('Kitty'); // setter: Kitty
  a.name = 'Tom'; // setter: Tom
  console.log(a.name); // Jack
  ```

+ 静态方法

  + 直接定义在类上，类直接访问
  + 属性方法定义在原型链上
  
  ```typescript
  class Animal {
      // static 静态方法  不需要实例化，而是直接通过类来调用 
      static isAnimal(a) {
          return a instanceof Animal;
      }
  }
  
  let a = new Animal('Jack');
  Animal.isAnimal(a); // true
  a.isAnimal(a); // TypeError: a.isAnimal is not a function
  ```

#### `ES7` 中类的用法

+ 实例属性

  ```typescript
  // ES6 中实例的属性只能通过构造函数中的 this.xxx 来定义，ES7 提案中可以直接在类里面定义：
  class Animal {
      name = 'Jack';
      constructor() {
          // ...
      }
  }
  let a = new Animal();
  console.log(a.name); // Jack
  ```

+ 静态属性

  + 通过类直接访问  
  
  ```typescript
  // ES7 提案中，可以使用 static 定义一个静态属性
  class Animal {
      static num = 42;
      constructor() {
          // ...
      }
  }
  console.log(Animal.num); // 42
  ```

#### `TS` 中类的用法

+ `public privite protected ` 访问修饰符

  + `public ` 定义公有的属性和方法，在类、子类及任何地方都可以访问到
  + `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问
  + `protected` 修饰的属性或方法为受保护的，只能在定义的类中和子类中访问

  ```typescript
  class Animal {
      public name;
      public constructor(name) {
          this.name = name;
      }
  }
  
  let a = new Animal('Jack');
  console.log(a.name); // Jack
  a.name = 'Tom';
  console.log(a.name); // Tom
  
  class Animal {
      // private 私有属性实例化后
      private name;
      public constructor(name) {
          this.name = name;
      }
  }
  
  let a = new Animal('Jack');
  console.log(a.name); // Jack
  a.name = 'Tom';
  
  class Animal {
      // private 私有属性子类中不能访问
      private name;
      public constructor(name) {
          this.name = name;
      }
  }
  
  // 子类无法访问name属性
  class Cat extends Animal {
      constructor(name) {
          super(name);
          console.log(this.name);
      }
  }
  
  class Animal {
      public name;
      // 当构造函数修饰为 private 时，该类不允许被继承或者实例化：
      private constructor (name) {
          this.name = name;
    }
  }
  // 出错
  class Cat extends Animal {
      constructor (name) {
          super(name);
      }
  }
  
  let a = new Animal('Jack');
  
  
  class Animal {
      public name;
      // 当构造函数修饰为 protected 时，该类只允许被继承：
      protected constructor (name) {
          this.name = name;
    }
  }
  class Cat extends Animal {
      constructor (name) {
          super(name);
      }
  }
  // 不能实例化
  let a = new Animal('Jack');
  
  ```

+ 修饰符可直接使用在构造函数

  ```typescript
  class Animal {
      // public name: string;
      public constructor (public name) {
          this.name = name;
      }
  }
  ```

+ `readonly`

  ```typescript
  // 只读属性关键字，只允许出现在属性声明或索引签名(构造函数)中。
  class Animal {
      readonly name;
      public constructor(name) {
          this.name = name;
      }
  }
  
  let a = new Animal('Jack');
  console.log(a.name); // Jack
  a.name = 'Tom';
  
  // index.ts(10,3): TS2540: Cannot assign to 'name' because it is a read-only property.
  
  // 注意如果 readonly 和其他访问修饰符同时存在的话，需要写在其后面。
  class Animal {
      // public readonly name;
      public constructor(public readonly name) {
          this.name = name;
      }
  }
  ```

+ 抽象类

  `abstract`  用于定义抽象类和其中的抽象方法，抽象类不允许实例化，抽象类中的抽象方法只能在子类中实现

  ```typescript
  abstract class Animal {
      public name;
      public constructor(name) {
          this.name = name;
      }
      // 抽象方法必须在子类中实现
      public abstract sayHi();
  }
  
  class Cat extends Animal {
      public eat() {
          console.log(`${this.name} is eating.`);
      }
  }
  
  let cat = new Cat('Tom');
  
  // index.ts(9,7): error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.
  ```

+ 类的类型

  ```typescript
  class Animal {
      name: string;
      constructor(name: string) {
          this.name = name;
      }
      sayHi(): string {
        return `My name is ${this.name}`;
      }
  }
  
  let a: Animal = new Animal('Jack');
  console.log(a.sayHi()); // My name is Jack
  ```

+ 类实现接口

  - `implements` 关键字实现 把类的特性提取成接口

  ```typescript
  interface Alarm {
      alert();
  }
  
  class Door {
  }
  
  class SecurityDoor extends Door implements Alarm {
      alert() {
          console.log('SecurityDoor alert');
      }
  }
  
  class Car implements Alarm {
      alert() {
          console.log('Car alert');
      }
  }
  ```

  - 一个类可以实现多个接口

    ```typescript
    interface Alarm {
        alert();
    }
    // 接口可以在面对对象编程中表示行为的抽象 
    interface Light {
        lightOn();
        lightOff(words: string): void;
    }
    
    class Car implements Alarm, Light {
        alert() {
            console.log('Car alert');
        }
        lightOn() {
            console.log('Car light on');
        }
        lightOff(words: string) {
            console.log('Car light off');
        }
    }
    ```

  - 接口继承接口

    ```typescript
    interface Alarm {
        alert();
    }
    
    interface LightableAlarm extends Alarm {
        lightOn();
        lightOff();
    }
    ```

  - 接口继承类

    ```typescript
    class Point {
        x: number;
        y: number;
    }
    
    interface Point3d extends Point {
        z: number;
    }
    
    let point3d: Point3d = {x: 1, y: 2, z: 3};
    ```

  - 接口定义函数 函数拥有自己的属性和方法

    ```typescript
    interface Counter {
        (start: number): string;
        interval: number;
        reset(): void;
    }
    
    function getCounter(): Counter {
        let counter = <Counter>function (start: number) { };
        counter.interval = 123;
        counter.reset = function () { };
        return counter;
    }
    
    let c = getCounter();
    c(10);
    c.reset();
    c.interval = 5.0;
    ```

#### 泛型

+ 同一个函数接受不同类型参数的一种模板

+ 明确定义的类型

+ 使用时指定类型

  ```typescript
  function _array(length: number, value:any): Array<any> {
   let result = []
   for (let i = 0; i < length; i++) {
    result[i] = value
   }
   return result
  }
  
  let res = _array(4,3)
  console.log(res)
  ```

+ 使用泛型

  ```typescript
  // T 类型变量 决定泛型指向那种类型
  function _array<T>(length: number, value:T): Array<T> {
    let result: T[] = []
    for (let i = 0; i < length; i++) {
     result[i] = value
    }
    return result
   }
   
   let res = _array<string>(4,'q')
   console.log(res)
  ```

+ 多个类型参数

  ```typescript
  function swap<T, U>(tuple: [T, U]): [U, T] {
      return [tuple[1], tuple[0]];
  }
  
  swap([7, 'seven']); // ['seven', 7]
  ```

+ 泛型约束

  ```typescript
  interface Lengthwise {
      length: number;
  }
  // 参数arg必须包含length属性
  function loggingIdentity<T extends Lengthwise>(arg: T): T {
      console.log(arg.length);
      return arg;
  }
  
  // 泛型约束 参数类型
  function getValue<T, K extends keyof T>(obj: T, key: K) {
      return obj[key]
  }
  const obj = {a:1}
  getValue(obj, b) // 报错
  ```

  - 多个类型参数之间也可以互相约束

    ```typescript
    function copyFields<T extends U, U>(target: T, source: U): T {
        for (let id in source) {
            target[id] = (<T>source)[id];
        }
        return target;
    }
    let x = { a: 1, b: 2, c: 3, d: 4 };
    copyFields(x, { b: 10, d: 20 });
    ```

+ 泛型接口

  ```typescript
  interface CreateArrayFunc {
      <T>(length: number, value: T): Array<T>;
  }
  
  let createArray: CreateArrayFunc;
  createArray = function<T>(length: number, value: T): Array<T> {
      let result: T[] = [];
      for (let i = 0; i < length; i++) {
          result[i] = value;
      }
      return result;
  }
  
  createArray(3, 'x'); // ['x', 'x', 'x']
  
  // 泛型接口
  interface Identities <T, U> {
      name: T,
      age; U
  }
  
  function identity<T, U>(name: T, age: U): Identities<T, U> {
      let identity: Identities<T, U>= {
          name,
          age
      }
      return identity
  }
  identity('fang', 26) // 推论泛型中的数据类型
  ```

  - 泛型参数提前到接口名

    ```typescript
    interface CreateArrayFunc<T> {
        (length: number, value: T): Array<T>;
    }
    
    let createArray: CreateArrayFunc<any>;
    createArray = function<T>(length: number, value: T): Array<T> {
        let result: T[] = [];
        for (let i = 0; i < length; i++) {
            result[i] = value;
        }
        return result;
    }
    
    createArray(3, 'x'); // ['x', 'x', 'x']
    ```

+ 泛型类

  ```typescript
  class GenericNumber<T> {
      zeroValue: T;
      add: (x: T, y: T) => T;
  }
  
  let myGenericNumber = new GenericNumber<number>();
  myGenericNumber.zeroValue = 0;
  myGenericNumber.add = function(x, y) { return x + y; };
  ```

+ 泛型参数的默认参数

  ```typescript
  function createArray<T = string>(length: number, value: T): Array<T> {
      let result: T[] = [];
      for (let i = 0; i < length; i++) {
          result[i] = value;
      }
      return result;
  }
  ```

  

#### 类型守卫

+ 类型保护：确保该类型在一定的范围内
+ `in` 关键字
+ `typeof` 关键字
+ `instanceof` 关键字

#### ` ECMAScript ` 私有字段

+ 与 `private` 不同

+ `#` 开头 私有字段

+ 不能在包含的类之外访问

+ 不能在私有字段上使用`ts` 的可访问性修饰符

  ```js
  class Person {
    #name: string;
  
    constructor(name: string) {
      this.#name = name;
    }
  
    greet() {
      console.log(`Hello, my name is ${this.#name}!`);
    }
  }
  
  let semlinker = new Person("Semlinker");
  
  semlinker.#name;
  //     ~~~~~
  // Property '#name' is not accessible outside class 'Person'
  // because it has a private identifier.
  ```

  

#### 装饰器

+ 表达式执行后返回一个函数
+ 函数的入参分别为 `target` `name` `descriptor`
+ 执行该函数后可能返回 `descriptor` 对象 用于配置 `target` 对象
+ 分类
  + 类装饰器
  + 属性装饰器
  + 方法装饰器
  + 参数装饰器

#### `interface/type` 接口和类型别名

+ 接口和类型别名都可以用来描述对象的形状或函数签名

  ```ts
  // 接口可定义多次会自动合并
  interface Point { x: string }
  interface SetPoint { (x: number, y: number): void}
  type Point = { x: string }
  type SetPoint = (x: number, y: number) => void
  ```

+ 类型别名可用于其他类型，原始类型、联合类型、元组

  ```ts
  // primitive
  type Name = string;
  
  // object
  type PartialPointX = { x: number; };
  type PartialPointY = { y: number; };
  
  // union 联合类型
  type PartialPoint = PartialPointX | PartialPointY;
  
  // tuple 元组类型
  type Data = [number, string];
  ```

+ 继承 

  + 接口和类型别名不是互斥的。接口可以扩展类型别名

  ```ts
  // 继承
  interface Point { x: string }
  interface SetPoint extends Poing {
      y: number
  } 
  
  type Point = { x: string }
  type SetPoint = Point & { y: string }
  
  // 接口继承类型别名
  type PartialPointX = { x: number; };
  interface Point extends PartialPointX { y: number; }
  
  // 类型别名继承接口
  interface PartialPointX { x: number; }
  type Point = PartialPointX & { y: number; };
  ```

+ 实现

  + 类可以以相同的方式实现接口和类型别名，但类不能实现类型别名定义的联合类型

  ```ts
  interface Point {
    x: number;
    y: number;
  }
  
  // 实现 implements
  class SomePoint implements Point {
    x = 1;
    y = 2;
  }
  
  type Point2 = {
    x: number;
    y: number;
  };
  
  class SomePoint2 implements Point2 {
    x = 1;
    y = 2;
  }
  
  type PartialPoint = { x: number; } | { y: number; };
  
  // A class can only implement an object type or 
  // intersection of object types with statically known members.
  class SomePartialPoint implements PartialPoint { // Error
    x = 1;
    y = 2;
  }
  ```

+ 接口可以重复定义，会自动合并为单个接口

#### 常见的内置类型别名

+ `Partial` 某个类型的属性全部变成可选项？

  ```ts
  type Partial<T> = {
      [P in keyof T]?: T[P]
  }
  // keyof T 拿到T的所有属性名 使用in进行遍历 将值赋给P
  
  interface Todo {
      title: string,
      desc: string
  }
  
  function updateTodo(todo: Todo, update: Partial<Todo>) {
      return {...todo, ...update}
  }
  // update 参数中的属性可选不需要匹配Todo接口定义的类型
  ```

+ `Required` 类型中的属性全部变成必选项

  ```ts
  type Required<T> = {
      [P in keyof T]-?: T[P];
  };
  // -? 移除 ? 可选项 每项都必选
  
  interface Props {
      a?: number.
      b?: number
  }
  
  const obj: Required<Props> = {a: 5} //error missing b属性
  ```

+ `Readonly` 类型的所有属性变为只读属性

  ```ts
  type Readonly<T> = {
      readonly [P in keyof T]: T[P];
  };
  // -readonly 移除类型中只读属性
  
  interface Todo {
    title: string;
  }
  const todo: Readonly<Todo> = {
    title: "Delete inactive users"
  };
  todo.title = "Hello"; // Error: cannot reassign a readonly property
  ```

+ `Record` `Record<K extends keyof any, T>`  的作用是将 K 中所有的属性的值转化为 T 类型

  ```ts
  type Record<K extends keyof any, T> = {
      [P in K]: T;
  };
  
  interface PageInfo {
    title: string;
  }
  
  type Page = "home" | "about" | "contact";
  
  const x: Record<Page, PageInfo> = {
    about: { title: "about" },
    contact: { title: "contact" },
    home: { title: "home" }
  };
  ```

  

