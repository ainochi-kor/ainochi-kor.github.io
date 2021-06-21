---
layout: post
author: ainochi-kor
title: React/27. Static Type Checking
---

> Flow, TypeScript와 같은 정적 타입 체커들은 코드 실행전에 특정한 타입 문제를 찾아낸다. 또한 자동완성과 같은 기능을 추가하여 개발자의 작업 흐름을 개선하기도 한다. 이러한 이유로 큰 코드 베이스에서는 PropTypes를 사용하는 대신 **Flow 혹은 TypeScript를 사용하는 것을 추천**한다.

---

## Flow 

Flow는 JS코드를 위한 정적 타입 체커이다. 페이스북에서 개발하였으며, 보통 React와 함께 사용한다. 특별한 타입 문법을 사용하여 변수, 함수 및 React 컴포넌트에 주석을 달 수 있고, 에러를 조기에 발견 가능하다.  
  
Flow를 사용하기 위해서는 아래 요구사항을 만족해야 한다.

- Flow를 프로젝트 의존성 추가
- 컴파일된 코드에서 Flow 문법이 제거되었는지 확인.
- 타입 주석을 추가하고, 타입을 체크하기 위해 Flow 실행

### 프로젝트에 Flow 추가하기 [npm]

#### 설치하기.
> \> npm  install --save-dev flow bin

#### package.json에 "flow" 추가하기 

``` json
// ...
"script": {
  "flow": "flow",
  // ...
  },
  // ...
}
```

#### flow 환경 설정 파일 생성

> \> npm run flow init


### 컴파일된 코드에서 Flow 문법 제거 [Babel]

#### preset 설정

> \> npm instaill --save-dev @babel/preset-flow

#### Babal configuration 추가

``` babelrc
{
  "presets": [
    "@babel/preset-flow",
    "react"
  ]
}
```

### Flow 실행하기 [npm]

> \> npm run flow


> **결과**
> No errors!

### Flow 타입 주석 추가하기

> //@flow 

대체적으로 위 주석은 파일 최상단에 두어야 한다. 

---

## TypeSrcipt

TypeScript는 MS가 개발한 프로그래밍 언어이다. JS의 타입 슈퍼셋이며 자체 컴파일러를 가지고 있다.  
  
TypeScript를 사용하기 위해서는 아래 요구사항을 만족해야 한다.

- 프로젝트 의존성에 TypeScript 추가
- TypeScript 컴파일러 옵션을 설정
- 올바른 파일 확장을 사용
- 사용하는 라이브러리의 정의를 추가

### 프로젝트에 TypeScript 추가하기 [npm]

#### 설치하기

> \> npm install --save-dev typescript

#### package.json파일 "script"부분에 "tsc"추가하기

``` json
{
  // ...
  "script" : {
    "build" : "tsc",
    // ...
  },
  // ...
}
```

### TypeScript 컴파일러 설정하기

TypeScript는 tsconfig.json 이라는 특별한 파일에 설정을 해야한다.

> \> npx tsc --init

실제로 컴파일러는 TypeScript 파일을 통해 JS파일을 생성한다. 여기서 소스파일과 생성된 파일간의 혼동을 야기할 수 있다.  
  
#### 해결 방법
- 우선 프로젝트 구조를 아래와 같이 정리한다. 모든 소스코드는 src 디렉토리에 위치시킬 것

``` 
├── package.json
├── src
│   └── index.ts
└── tsconfig.json
```

- 그 다음, 소스코드가 어디 있는지, 컴파일을 통해 생성된 코드를 어디에 위치시켜야 하는지 컴파일러에 서술.

``` json
// tsconfig.json 

{
  "compilerOptions" : {
    // ...
    "rootDir": "src",
    "outDir": "build"
    // ...
  }
}
```

일반적으로 build 폴더를 .gitignore 파일에 추가한다.

### 파일 확장자

React에서는 대부분의 컴포넌트를 .js 파일에 작성한다.  
TypeScript에는 두가지 확장자가 있다.  
  
- .ts는 TypeScript 파일 확장자 기본 값.
- .tsx는 JSX문법이 포함된 코드를 위한 특별한 확장자.

### TypeScript 실행하기

> \> npm run build 

터미널에 아무런 출력이 없다면 컴파일이 성공적으로 완료됨을 의미한다.

### 타입 정의

다른 패키지의 오류와 힌트를 출력하기 위해 컴파일러는 선언 파일에 의존한다. 선언 파일은 라이브러리에 대한 모든 타입 정보를 제공한다. 프로젝트의 npm에 라이브러리에 대한 선언 파일이 있다면 해당 JS 라이브러리를 사용할 수 있다.  
  
#### 라이브러리에 대한 선언을 가져올 수 있는 방법 두 가지

- Bundle : 라이브러리가 자신의 선언 파일을 번들한다. 라이브러리가 번들된 타입을 가지고 있는지 확인하려면 프로젝트 내 index.d.ts 파일이 존재하는지 확인하고, 어떤 라이브러리는 package.json 파일의 typings 혹은 types 필드 아래 정의되어 있다.

- Definitely Typed : 선언 파일을 번들하지 않은 라이브러리를 위한 거대 저장소. MS와 오픈 소스 기여자들에 의해 관리되는 클라우드 소스.

> \> npm i --save-dev @types/react

- Local Declarations : declarations.d.ts 파일을 source 디렉토리의 루트에 생성

``` ts
declare module 'quertystring' {
  export function stringify(val: object): string;
  export function parse(val: string): object;
}
```
