---
title:  "vue.js 초기 설정"
excerpt: "vue cli 초기 설정"

categories:
  - vuejs
tags:
  - [vuejs, javascript]

toc: true
toc_sticky: true
 
date: 2022-04-22
last_modified_at: 2022-04-22
---
# vue.js 초기 설정

## 1. Vue 프로젝트 생성 ddd

vue cli 설치 전 node.js를 먼저 설치 해야함

> **노드 버전 요구 사항**

Vue CLI 4.x에는 Node.js 버전 8.9 이상이 필요합니다(v10+ 권장). 
nvm 또는 nvm-windows를 사용하여 동일한 시스템에서 여러 버전의 Node를 관리할 수 있다.
> 

```bash
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

1. 터미널에서 프로젝트 생성 visual code studio 내에서 ctrl + ` 누르면 터미널 열림. cmd, powereshell 창 따로 열어서도 가능 
2. vue create [프로젝트 폴더 명]
Babel, Linter , Prettier, Lint on save, In dedicated config files 선택
    
![터미널 창에서 vue프로젝트 생성]({{ site.url }}{{ site.baseurl }}/assets/images/createvue.png)

터미널 창에서 vue프로젝트 생성

![vue 버전 선택 마지막에 있는 수동 선택]({{ site.url }}{{ site.baseurl }}/assets/images/menualSelect.png)

vue 버전 선택 마지막에 있는 수동 선택
    

![원하는 초기 세팅을 선택, 나중에 추가 할수 있음]({{ site.url }}{{ site.baseurl }}/assets/images/libSelect.png)

원하는 초기 세팅을 선택, 나중에 추가 할수 있음

![vue 버전 선택]({{ site.url }}{{ site.baseurl }}/assets/images/vueVersinSelect.png)

vue 버전 선택

![router를 선택 하면 history  mode 사용하냐고 묻는다. yes 선택]({{ site.url }}{{ site.baseurl }}/assets/images/modeSelect.png)

router를 선택 하면 history  mode 사용하냐고 묻는다. yes 선택

![프로젝트 생성 후 초기 세팅]({{ site.url }}{{ site.baseurl }}/assets/images/afterCreate.png)

프로젝트 생성 후 초기 세팅

## 2. 초기 세팅파일

1. vue.config.js 생성(root)

```jsx
//웹팩 deveserver overlay 오프
module.exports = {
	devServer: {
            overlay: false,
	},
};
```

1. .eslintrc.js

```jsx
rules: {
	"no-console": "off",	// console.log() 사용시에러 나는거 제거
	// "no-console": process.env.NODE_ENV === "production" ? "warn" : "off",
	// "no-debugger": process.env.NODE_ENV === "production" ? "warn" : "off",
},
```

.prettier 설정 : eslint 랑 겹치기 때문에 eslint가 우선 적용되야 하기때문

>참고 :
[https://joshua1988.github.io/web-development/vuejs/boost-productivity/](https://joshua1988.github.io/web-development/vuejs/boost-productivity/)

.eslintrc.js

```jsx
module.exports = {
// 현재 eslintrc 파일을 기준으로 ESLint 규칙을 적용
root: true,
// 추가적인 규칙들을 적용
extends: [
	'eslint:recommended',
	'plugin:vue/essential',
	'prettier',
	'plugin:prettier/recommended',
],
// 코드 정리 플러그인 추가
plugins: ['prettier'],
// 사용자 편의 규칙 추가
rules: {
	'prettier/prettier': [
	'error',
	// 아래 규칙들은 개인 선호에 따라 prettier 문법 적용
	// [https://prettier.io/docs/en/options.html](https://prettier.io/docs/en/options.html)
	{
		singleQuote: true,
		semi: true,
		useTabs: true,
		tabWidth: 2,
		trailingComma: 'all',
		printWidth: 80,
		bracketSpacing: true,
		arrowParens: 'avoid',
		endOfLine: 'auto',
	},
	],
	'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
	},
};
```
위 사항 적용 안될때
WINDOW 10 : file -> preference -> settings -> eslint 검색
    
-  Eslint : Probe -> 자바스크립트,vue,html 있는지 확인
-  Eslint : Validate -> settings.json 편집 클릭

```jsx
 "eslint.validate": [
		{
        "language": "vue",
        "autoFix": true
    },
    {
        "language": "javascript",
        "autoFix": true
    },
    {
        "language": "javascriptreact",
        "autoFix": true
    },
    {
        "language": "typescript",
        "autoFix": true
    },
    {
        "language": "typescriptreact",
        "autoFix": true
    }
],
"editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
},
// don't format on save
"editor.formatOnSave": false,

```

추가 해줌

1. settings -> format save 검색 체크해제 prettier랑 충돌 방지
2. 절대경로 설정
root 경로에 jsconfig.json 파일 생성
```jsx
{
    "compilerOptions": {
      "baseUrl": ".",
      "paths": {
        "~/*": [
          "./*"
        ],
        "@/*": [
          "./src/*"
        ],
      }
    },
    "exclude": [
      "node_modules",
      "dist"
    ]
  }
```
>참고 : 
[https://code.visualstudio.com/docs/languages/jsconfig](https://code.visualstudio.com/docs/languages/jsconfig)
