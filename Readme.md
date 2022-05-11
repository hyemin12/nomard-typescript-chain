# Typescript

타입스크립트를 이용하는 가장 큰 이유는 안정성( 타입 안정성 )

- 버그를 줄일 수 있음
- 생산성을 높일 수 있음

- 변수의 타입을 지정

```js
let b = [1, 2, 3];
// number[]

const c = {
  name: "nico",
};
// player: { name: string; }
```

<br>
<hr>
<br>

## 타입스크립트 문법

<br>
 : 타입

```js
let a: boolean = false;
```

# 타입스크립트 데이터

### const 나 let같은 변수에 type 할당하기

```js
let a: number = 1;
let b: string = "hi";
let c: boolean = true;
let d: number[] = [1, 2, 3];
```

오브젝트 타입 결정(name은 항상 가지고 있고, age는 있거나 없는 경우)

```js
let player: { name: string, age?: number } = {
  name: "nico",
};
// ?: number || undefined
```

Alias 별칭 사용

```js
type Player = { name: string, age?: number };

const playerNico: Player = {
  name: "nico",
};
const playerHyemin: Player = {
  name: "hemin",
  age: 2,
};
```

### 함수가 return 하는 값에 type 할당하기

1. 인수에서 이름 쓰고 타입 작성<br>
   (name: string)

2. 함수에서 뒤에 타입 작성<br>
   function playerMaker(): Player

```js
type Player = { name: string, age?: number };

function playerMaker(name: string): Player {
  return {
    name,
  };
}
// function playerMaker(name: string) : Player
// return 값 Player
const hyemin = playerMaker("hyemin");
hyemin.age = 12;
```

- 화살표 함수

```js
const playerMaker = (name: string): Player => ({ name });

const hyemin = playerMaker("hyemin");
hyemin.age = 12;
```

## readonly 속성을 타입에 추가

읽기전용 속성 (자바스크립트에는 없음)
수정 불가능

```js
type Age = number;
type Name = string;
type Player = {
  readonly name: Name,
  age?: Age,
};

const playerMaker = (name: string): Player => ({ name });
const hyemin = playerMaker("hyemin");
hyemin.age = 12;

// Error!
hyemin.name = 'hahaha'
```

```js
const numbers: readonly number[] = [1,2,3,4]
// 속성을 변경하지 않는 메소드는 사용 가능
numbers.filter()
numbers.map()

// Error!
numbers.push(1)
```

## Tuple

항상 정해진 갯수의 요소를 가져야하는 array를 지정할 수 있음

```js
const player: [string, number, boolean] = ["hyemin", 12, true];

// Error! 첫번째 인덱스는 string이어야하기때문
player[0] = 1;

const player2: readonly [string, number, boolean] = ["hyemin", 12, true];
```

## any

비어있는 값의 기본값
타입스크립트로 부터 빠져나오고싶을 때 쓰는 타입

```js
let a = [];
// a: any
```

## void

아무것도 return하지 않는 함수를 대상으로 사용

```js
function hello() {
  console.log("x");
}
// function hello(): void

const a = hello();

// Error!
a.toUpperCase();
```

## never

함수가 절대 return 하지 않을 때 사용

```js
function hello(): never {
  throw new Error("xxx");

  // Error!
  return "X";
}
```

타입이 두가지 일 수도 있는 상황에 발생하기도 함

```js
function hello(name:string|number)){
  if(typeof name === 'string'){
    // name : string
    name
  } esle if(typeof name === 'number'){
    // name : number
    name
  } else{
    // name : never
    // 절대 실행되지 않아야 함
    name }


  // Error!
  // name은 string 일수도 있고, number일 수도 있기때문
  name + 1
}
```

## unknown

어떤 타입인지 모르는 변수에 사용

어떤 작업을 하려면 이 변수의 타입을 먼저 확인해야함

```js
let a: unknown;

if (typeof a === "number") {
  // a의 타입이 number인 것을 확인했기 때문에 다음 코드 실행
  let b = a + 1;
} else if (typeof a === "string") {
  let b = a.toUpperCase();
}
```

## function

### call signature

함수 위에 마우스를 올렸을 때 보게 되는 것 함수의 인자의 타입, 함수의 반환타입을 알려줌

call signature 적용

```js
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => a + b;
```

<br>
<br>

이전버전

```js
// a: number , b: number , return: number
function add(a: number, b: number) {
  return a + b;
}

const add = (a: number, b: number) => {
  return a + b;
};
```

<br>
<hr>
<br>

### 오버로딩 overloading

함수가 <b>서로 다른</b> 여러개의 call signatures를 가지고 있을 때 발생

string을 보내거나 config 객체를 보내는 경우

```js
type Config = {
  path: string,
  state: object
}

type Push={
  (path:string):viod
  (config: Config): void
}

const push:Push = (config)=>{
if(typeod config === 'string'){
  console.log(config)
}
else{console.log(config.path, config.state)}
}
```

<br>
<br>

여러개의 인자를 가지고 있는 경우

```js
type Add = {
  (a:number, b:number): number
  (a:number, b:number, c:number): number
}
// c: option (추가 파라미터 옵션)

const add:Add =(a,b,c?:number) => {
  if(c) return a + b + c
  return a+ b
}

//Error! 파라미터의 개수가 다르기때문
const add:Add =(a,b,c) => {
  return a+ b
}
```

<br>
<hr>
<br>

### 제네릭generics

- 콘크리트 타입을 사용하는 것 대신 쓸 수 있음
  콘크리트 타입(number, string, boolean, unknown 등)

- 타입의 placeholder와 같은 것(타입을 유추)
- call signature를 작성하는데 concrete type을 알 수 없을 때 사용

* 사용방법

타입스크립트에 generic을 사용하겠다고 선언

```js
// call signature
type Typename = {
  <GenericName>(arg: GenericName): void,
  <T,M>(a:T, b:M)=>T
};

const superPrint: SuperPrint = (arr) => arr[0];
};

// 함수형
function superPrint<V>(a:V[]){
  return a[0]
}
```

<br>

예시1)

```js
type Player<Extra> = {
  name: string
  extranInfo: Extra
}
type MarkExtra = Player<{favFood:string}>>
const mark: MarkExtra  = {
  name: mark,
  extraInfo: {
    favFood: 'watermelon'
  }
}

const nico: Player<null> = {
  name: nico,
  extraInfo: null
}
```

<br>
<br>

예시2)

```js
type A = Array<number>;

let a: A = [1, 2, 3];

function printAllNumbers(arr: Array<number>) {
  return;
}
```

<br>
<br>

예시3)

```js
useState<number>()
```

<br>
<hr>
<br>

### 다향성 polymorphism

call signature을 하나하나 만들어서 다양한 형태를 받음

```js
type SuperPrint = {
  (arr: number[]):void
  (arr: boolean[]):void
  (arr: string[]):void
}

const superPrint: SuperPrint = (arr)=>{
  arr.forEach(i=>console.log(i))
}

superPrint([1,2,3])
superPrint([true, false, true])
superPrint(['a', 'b', 'c'])

// Error!
superPrint([1, 2, true, 'c'])
```

generic을 사용하여 다양한 형태를 받음

```js
type SuperPrint = {
  <GenericType>(arr: GenericType[]): void,
  // <T, V>(arr: number[]):void
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

superPrint([1, 2, 3]);
superPrint([true, false, true]);
superPrint(["a", "b", "c"]);
superPrint([1, 2, true, "c"]);
```

```js
type SuperPrint = {
  <GenericType>(arr: GenericType[]): GenericType,
};
const superPrint: SuperPrint = (arr) => arr[0];
};

const a = superPrint([1, 2, 3]);
// a: number
const b = superPrint([true, false, true]);
// b: boolean
const c = superPrint(["a", "b", "c"]);
// c: string
const d = superPrint([1, 2, true, "c"]);
// d: number | boolean | string
```

## classes and interfaces

### classes

```js
// typescript

class Player {
  constructor(
    private firstName: string,
    private lastName: string,
    public nickName: string
  ){}
}

const mark = new Player('mark', 'lee', '마끄')

// Error! firstName is private
mark.firstName
```

```js
// javascript

class Player extends User {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}

const mark = new Player("mark", "lee", "마끄");
mark.firstName;
```

<br>
<br>

#### 추상 클래스

<br>

다른 클래스가 상속받을 수만 있는 클래스<br>
새로운 인스턴스를 만들수는 없음<br>
<br>

- 추상메소드<br>
  추상 클래스 안에 추상 메소드를 만들 수는 있지만, 구현을 하면 안됨<br>
  대신, 메소드의 call signature만 적어야함<br>
  <br>
  <br>
  private: 가장 강력한 보호, 다른 클래스에서 사용 불가 <br>
  protected: 보호하면서 상속하는 다른 클래스에서도 사용이 가능 <br>
  public: 기본값 , 외부에서 사용가능
  <br>
  <br>

```js
// typescript

abstract class User{
 constructor(
    private firstName: string,
    protected lastName: string,
    public nickName: string
  ){}
 // 추상 메소드
 abstract getNickName(): void

  getFullName(){
    return `${this.firstName} ${this.lastName}`
  }
}

class Player extends User {
  getNickName(){
    console.log(`${this.nickName} ${this.lastName}`)
  }
}

const mark = new Player('mark', 'lee', '마끄')

mark.getFullName()
```

```js
// javascript

class User {
  constructor(firstName, lastName, nickName) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.nickName = nickName;
  }
  getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}
class Player extends User {
  getNickName() {
    console.log(`${this.nickName} ${this.lastName}`);
  }
}
const mark = new Player("mark", "lee", "마끄");
mark.getFullName();
```

<br>
<hr>
<br>

#### 해시맵 만들기

```js
// 오로지 string만 가지는 string 오브젝트
// 제한된 양의 property 혹은 key를 가지는 타입을 정의하는 방법
type Words = {
  [key:string]:string
}

class Dictionary{
  private words: Words
  constructor(){
    // 초기화
    this.words ={}
  }
  // 클래스를 Type 처럼 사용
  add(word:Word){
    if(this.words[word.term]===undefined){
      this.words[word.term] = word.def
    }
  }
  def(term:string){
    return this.words[term]
  }
  delete(word: Word){
    if(this.words[word.term]){
      delete this.words[word.term];
    }
  }
  update(term: string, newDef:string){
    if(this.words[term] !== undefined ){
    this.words[term] = newDef
    }
  }
}

class Word {
  constructor(public term:string, public def:string){}
}

const kimchi = new Word ('kimchi','한국의 음식')

const dict = new Dictionary()

dict.add(kimchi)
dict.def('kimchi')
```

<br>
<hr>
<br>

### interfaces

오브젝트의 모양을 특정해줄 때만 사용가능<br>
React.js에서 많이 사용함<br>
type 보다 객체지향형 프로그래밍과 유사<br>

<br>
<br>

#### Type

```js
// 특정 string
type Team = "red" | "blue" | "yellow";
type Health = 1 | 5 | 10;

// type version
type Player = {
  nickname: string,
  team: Team,
  health: Health,
};

// interface version
interface Player {
  nickname: string;
  team: Team;
  health: Health;
}

const mark: Player = {
  nickname: "mark",
  team: "blue",
  health: 5,
};
```

<br>

```js
// interface
interface User {
  name: string
}

interface Player extends User {
}
const mark: Player {
  name: 'mark'
}
```

```js
// type
type User = {
  name: string
}

type Player =  User &  {
}
const mark: Player {
  name: 'mark'
}
```

#### 각각 인터페이스를 만들어도, 타입스크립트가 하나로 합쳐줌 (type은 불가능)

<br>

```js
interface User {
  name: string;
}
interface User {
  lastName: string;
}
interface User {
  health: number;
}

const person: User = {
  name: "name",
  lastName: "lastname",
  health: 1,
};

// Error!
type User = {
  name: string,
};
type User = {
  lastName: string,
};
```

<br>

#### class & interface

```js
// 추상 클래스
abstract class User {
  constructor (
    protected firstName: string,
    protected lastName: string
  ){}
  abstract sayHi(name:string): string
  abstract fullName(): string
}
class Player extends User {
  // sayHi, fullName 필수 작성
  fullName(){
    return `${this.firstName} ${this.lastName}`
  }
  sayHi(name:string){
    return `Hello ${name}. my Name is ${this.fullName()}`
  }
}
```

인터페이스를 사용하면 추상클래스가 자바스크립트 코드로 컴파일 되지 않음
implements: 인터페이스 상속, 코드가 더 가벼워짐

```js
interface User {
  firstName: string,
  lastName: string
  sayHi(name:string): string
  fullName(): string
}
interface Human {
  health: number
}

class Player implements User, Human {
  constructor(
    public firstName: string,
    public lastName: string,
    public health: number
  ){}
  fullName(){
    return `${this.firstName} ${this.lastName}`
  }
  sayHi(name:string){
    return `Hello ${name}. my Name is ${this.fullName()}`
  }
}

// interface를 argument로 넣어줄 때
function makeUser(user: User){
  return 'Hi'
}
makeUser({
  firstName: 'name',
  lastName:'aaa',
  fullName: ()=>'string',
  sayHi: (name)=> 'string'
})
```

# 타입스크립트 프로젝트 시작하기

### typescript설치

npm i -D typescript
<br>

### package.json 초기화

npm init -y
<br>

### tsconfig.json설정

디렉터리에 tsconfig.json 파일이 있으면 해당 디렉터리가 TypeScript 프로젝트의 루트임을 나타냅니다. tsconfig.json 파일은 프로젝트를 컴파일하는 데 필요한 루트 파일과 컴파일러 옵션을 지정합니다.
https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#handbook-content
<br>

### Target (기본값: ES3)

최신 브라우저는 모든 ES6 기능을 지원하므로 ES6는 좋은 선택입니다. 코드가 이전 환경에 배포된 경우 더 낮은 target을 설정하거나 최신 환경에서 코드 실행이 보장되는 경우 더 높은 target을 설정하도록 선택할 수 있습니다.
ex) 화살표 함수() => this는 ES5 이하이면 함수 표현식으로 바뀝니다.
<br>
<br>

## 프로젝트를 타입스크립트로 만들기

### 1. 파일 전체를 위한 타입 정의 생성(myPackage.d.ts)

정의파일<br>
호출 시그니처, 타입 작성 (구현 X)

```js
interface Config {
  url: string;
}
declare module "myPackage" {
  function init(config: Config): boolean;
  function exit(code: number): number;
}
```

<br>
<br>

### 2. JSDoc

함수 바로위에 코멘트
코멘트를 제대로 작성하면 타입스크립트가 코멘트를 읽을 수 있음
코드를 모두 타입스크립트로 바꿀수는 없음

```js
// @ts-test
/**
 * Initializes  the project
 * @param {object} config
 * @param {boolean} config.debug
 * @param {string} config.url
 * @returns {void}
 */

export function init(config) {
  return true;
}

/**
 *  Exits the project
 * @param {number} code
 * @returns {number}
 */

export function exit(code) {
  return code + 1;
}
```

### ts-node

빌드 없이 타입스크립트를 실행할 수 있게 해줌
빌드 없이 빠르게 새로고침하고 싶을 때 사용

<br>
패키지 설치 
```
npm i -D ts-node
```

```js
// package.json
"scripts": {
  "build": "tsc",
  "dev": "ts-node src/index",
  "start": "node build/index.js"
}
```

- nodemon 설치했을 경우

```
npm i nodemon
```

```js
// package.json
"scripts": {
  "build": "tsc",
  "dev": "nodemon --exec ts-node src/index.ts",
  "start": "node build/index.js"
}
```

<br>

실행 명령어

```
npm run dev
```

<br>
<br>

- 기존 빌드 방식

```js
// package.json
"scripts": {
  "build": "tsc",
  "start": "node build/index.js"
}
```

```
npm build && npm start
```

## 모듈 가져오는 방법

### 1. import \* as 패키지 from '경로'

```js
import * as crypto from "crypto";
```

### 2. tsconfig 파일 내 rule 추가

```js
// tsconfig.json

"compilerOptions": {
    "esModuleInterop": true,
    "module": "CommonJS"
  }
```

<br>
<hr>
<br>

## 타입스크립트로 작성되지 않은 패키지 import 할 때 나타나는 오류

(타입 정의가 되어있지 않을 때)

definitelyTyped
https://github.com/DefinitelyTyped/DefinitelyTyped

여러 개발자들이 타입을 미리 정의 해놓은 레포지토리

<br>
<br>

```
npm i -D @types/node
```

```
npm i -D @types/패키지명
```
