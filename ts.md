# Typescript

- Superset of Javascript.
- Adds strict and static types.

Installing and Running
`npm install -g typescript`
`tsc main.ts`

### Initializing TypeScript Project

`tsc --init`

`tsconfig.json` file generated.

Compile Code
`tsc`

Watch Mode
`tsc --watch` // re trigger compilation

Installing Packages
`npm init -y`

`package.json`
Just change `"type": "module"`, so that we can use modern import instead of require import.

### Investment Calculator Project

```ts
type InvestmentData = {
    initialAmount: number;
    annualContribution: number;
    expectedReturnRate: number;
    durationInYear: number;
};

type InvestmentResult = {
    year: string;
    totalAmount: number;
    totalContribution: number;
    totalInterestEarned: number;
};

type CalculationResult = InvestmentResult[] | string;

function calculateInvestment(data: InvestmentData): CalculationResult {

    if (data.initialAmount <= 0) {
        return "Initial amount cannot be less than or equal to 0";
    }
    if (data.durationInYear <= 0) {
        return "Provide valid year";
    }
    if (data.expectedReturnRate <= 0) {
        return "Provide valid expected return rate";
    }

    const annualResults: InvestmentResult[] = [];

    let total = data.initialAmount;
    let totalContribution = 0;
    let totalInterestEarned = 0;

    // calculate return
    for (let i = 0; i < data.durationInYear; i++) {
        total = total * (1 + data.expectedReturnRate);
        totalInterestEarned = total - totalContribution - data.initialAmount;
        totalContribution = totalContribution + data.annualContribution;
        total = total + data.annualContribution;

        annualResults.push({
            year: `Year ${i + 1}`,
            totalAmount: total,
            totalContribution,
            totalInterestEarned,
        });
    }

    return annualResults;
}

function printResults(results: CalculationResult) {
    if (typeof results === 'string') {
        console.log(results);
        return;
    }

    for (const yearEndResult of results) {
        console.log(yearEndResult.year);
        console.log(`Total: ${yearEndResult.totalAmount.toFixed(0)}`);
        console.log(`Total Contributions: ${yearEndResult.totalContribution.toFixed(0)}`);
        console.log(`Total Interest Earned: ${yearEndResult.totalInterestEarned.toFixed(0)}`);
        console.log("-----------------------------");
    }
}
const investmentData: InvestmentData = {
    initialAmount: 5000,
    annualContribution: 1000,
    expectedReturnRate: 0.12,
    durationInYear: 10
};

const results = calculateInvestment(investmentData);
printResults(results);
```

### Classes
Basic Class
```ts
class User{
   name:string;
   age:number;

   constructor(name:string, age: number){
      this.name = name;
      this.age = age;
   }
} 
```
Alternative shortcut way to create class and properties
```ts
class User{
   constructor(public name:string, public age: number){}
} 

const user =  new  User("Sandeep",  35);
```
Note:
* `public` variables can be accessed outside the class and can be re-assigned.
* `private` variables can be accessed inside the class.
* `protected` variables can be accessed inside the class and classes which are inherited.

Readonly 
```ts
// we cannot re-asign, we can push new item to the array.
readonly hobbies:string[]  =  [];
```
### Getters
```ts
class User{
   constructor(private firstName:string, private lastName:string){}
   
   get fullName(){
      return this.firstName + ' ' + this.lastName;
   }
   
} 
const user = new User("Sandeep", "Dewangan");
console.log(user.fullName);
```
### Setters
```ts
class User{
   private _firstName = '';
   private _lastName = '';

   set firstName(fname:string){
      this._firstName = fname;
   }

   set lastName(lname:string){
      this._lastName = lname;
   }
} 

const user = new User();
user.firstName = 'Sandeep';
user.lastName = 'Dewangan';
```

### Static
```ts
class User{
   static userType = "Admin";

   static greet(){
      console.log("Hello");
   }
} 
// access
User.userType;
User.greet();
```

### Interfaces
Interfaces can be used as a object type and contracts that can be implemented by the classes.

```ts
interface Authenticable{
   email: string;
   password: string;

   login():void;
   logout():void;
}

let user : Authenticable;

user = {
   email: "sandeep@gmail.com",
   password: "abc",
   login(){
      // ....
   },
   logout(){
      // .....
   }
}
```
### Intersection Types
```ts
type FileData = {
    path: string;
}

type Status ={
    isOpen: boolean;
}
// It contains all the properties of FileData and Status
type AccessedFileData = FileData & Status;
```
### Type Guards
Discriminated Unions
```ts
type FileSource = { type: 'file'; path: string; }
type  DBSource = { type: 'db'; connectionString: string; }

type Source = FileSource | DBSource;

function loadData(source:Source){
    if(source.type === 'file'){
        // ....
    }else{
        // ....
    }
}
```
`instanceof` with classes
```ts
class User{}
class Admin{}

type Entity = User | Admin;

function init(entity: Entity){
    if(entity instanceof User){
        // ...
    }
} 
```

### Indexed Type - Dynamic Type
```ts
// [] placeholder for the dynamic property, in which we do not know 
// the var name in advance.
type DataSource = {
   [key: string]: string | boolean;
}

let data : DataSource = {};

// add property dynamically
data.id = '123';
data.isAdmin = false;
```

### Satisfies
Problem Code
```ts
const data: Record<string, number> = {
   home: 3,
   about: 5
};
// we can access
console.log(data.home);
console.log(data.kuchbi); // Doesnot throw error by ts
```
Solution
```ts
const data = {
   home: 3,
   about: 5
} satisfies Record<string, number>;
// we can access
console.log(data.home);
console.log(data.kuchbi); // Property 'kuchbi' does not exist on type
```

### Generics
Generics, that is, being able to create a component that can work over a variety of types rather than a single one.
```ts
type DataSource<T> = {
   [key:string]: T;
};

let data: DataSource<string|boolean> = {};
data.name = "Sandeep";
data.isAdmin = true;
```
### Generic Functions
```ts
function merge<T, S>(a:T, b:S){
   return [a, b];
}
const id = merge(1, 'Sandeep');
```
### Constraints on Generic
```ts
function merge<T extends object>(a:T, b:T){
   return {...a, ...b};
}
const user = merge({name:"sandeep"}, {age: 32});
console.log(user);
```
### Class Generics
```ts
class User<T>{
   constructor(public id:T){}
}
const u1 = new User('id1');
const u2 = new User(1);
```
### Derive Types from Types

### Typeof
The  `typeof`  operator can also be very useful for quickly getting the type of a (potentially complex) function.
```ts
const settings = {
   difficulty: "medium",
   level: 10,
   didStart: false
};

// without copying we can declare type

type Settings = typeof settings;

// this will parse to --> 
// type Settings = {
//     difficulty: string;
//     level: number;
//     didStart: boolean;
// }

function loadSettings(settings: Settings){
   // ...
}
```

### Keyof
```ts
function getProps<T extends object, U extends keyof T>(obj: T, key: U){
   // ...
}
```
### Indexed Access Types
```ts
type AppUser = {
   name: string;
   age: number;
   permissions: {
      id: string;
      title: string;
   }[];
}

// array of permissions
type Perms = AppUser['permissions'];

// type Perms = {
//     id: string;
//     title: string;
// }[]

// only one permission
type Perm = Perms[number];

// type Perm = {
//     id: string;
//     title: string;
// }
```
### Mapped Types
Map one object type to another.
```ts
type Operations = {
   add: (a: number, b: number) => number;
   substract: (a: number, b: number) => number;
}

// Instead of this
//	type Results = {
//    add: number;
//    substract: number;
// }

// Use this
type Results<T> = {
   [Key in keyof T]: number;
}

let mathOperations: Operations = {
   add(a:number, b: number){
      return a+b;
   },
   substract(a:number, b: number){
      return a-b;
   }
}

let mathResults: Results<Operations> = {
   add: mathOperations.add(1, 2),
   substract: mathOperations.substract(2, 3),
}
```
Readonly and Optional Mapping
```ts
type Results<T> = {
   readonly [Key in keyof T]?: number;
}
```
### Template Literal Types
```ts
type ReadPermissions = 'no-read' | 'read';
type WritePermissions = 'no-write' | 'write';

type FilePermissions = `${ReadPermissions}-${WritePermissions}`;

// type FilePermissions = 
//   "no-read-no-write" | "no-read-write" | "read-no-write" | "read-write"
```

## ECMAScripts Decorators
Decorators in TypeScript are a language feature that allows for the modification or annotation of classes and their members (methods, properties, accessors, or parameters) in a declarative way. They provide a form of metaprogramming, enabling the addition of functionality or modification of behavior at design time.

### Class / Method / Fields Decorator
```ts
// ------ Class Decorator ------
// new()=>any we want to interacts with a class which accepts any 
// type constructor arguments and returns any type.

function logger<T extends new (...args: any[])=> any>(target: T, context: ClassDecoratorContext){
	
    console.log("Class Decorator");
    console.log(target);
    console.log(context);

    // we can extend target class with a new var age=35
    return class extends target{
        constructor(...args: any[]){
            super(...args);
            console.log("Class Constructor");
            console.log(this);
        }
    }
}

// ------ Method Decorator ------
function autobind(target: (...args: any[]) => any, context: ClassMethodDecoratorContext){
    context.addInitializer(function (this: any){
        this[context.name] = this[context.name].bind(this);
    });
}

// ------ Field Decorator ------
function fieldLogger(target: undefined, context: ClassFieldDecoratorContext){
    return (initialValue: any) => '';
}

@logger
class User{¯
    @fieldLogger
    userName = "Sandeep";

    @autobind
    greet(){
        console.log("Hello there, "+ this.userName);
    }
}

const u1 = new User();
const greet = u1.greet;
greet();
```
