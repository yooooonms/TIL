## Vue.js 프로젝트 구조  

<img src="/Vue/image/vue-package-structure.png" width="300" height="550">  

- `node_modules`: npm으로 설치된 패키지 파일들이 모여 있는 디렉토리
- `public`: 웹팩(webpack)을 통해 관리되지 않는 정적 리소스가 모여 있는 디렉토리
- `src/assets`: 이미지, css, 폰트 등을 관리하는 디렉토리
- `src/componentes`: Vue 컴포넌트 파일이 모여 있는 디렉토리
- `App.vue`: 최상위 컴포넌트
- `main.js`: 가장 먼저 실행되는 자바스크립트 파일로 Vue 인스턴스를 생성하는 역할을 한다.
- `.gitignore`: 깃허브에 업로드 할 때 제외할 파일 설정
- `babel.config.js`: 바벨(Babel) 설정 파일
- `package-lock.json`: 설치된 package의 dependency 정보를 관리하는 파일
- `package.json`: 프로젝트에 필요한 package를 정의하고 관리하는 파일
- `README.md`: 프로젝트 정보를 기록하는 파일

#### # package.json  

```json
{
  "name": "frontend",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "core-js": "^3.6.5",
    "vue": "^3.0.0"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "@vue/compiler-sfc": "^3.0.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^7.0.0"
  },
  "eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/vue3-essential",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {}
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead"
  ]
}
```

- `name`: 프로젝트 이름을 입력
- `version`: 프로젝트 버전 정보를 입력
- `private`: true로 설정하면 해당 프로젝트를 npm으로 올릴 수 없다. 개발자가 실수로 해당 프로젝트를 npm으로 올리더라도 private이 true로 되어 있으면 배포를 막을 수 있다.
- `scripts`: 프로젝트 실행과 관련된 명령을 등록한다. 프로젝트 실행, 빌드 등 프로젝트 개발, 운영에 사용되는 다양한 script 명령어를 등록해 쉽게 사용할 수 있다. 개발자가 직접 정의한 script는 `npm run` 명령어로 사용하고 npm에서 제공되는 명령어는 `npm` 명령어로 사용한다.
- `dependencies`: 사용 중인 패키지 정보를 입력한다.
- `devDependencies`: 프로젝트 배포 시 필요 없는, 개발 시에만 필요한 패키지 정보가 등록된다.
- `eslintConfig` ESLint는 일관성 있게 코드를 작성하고 버그를 식별하고 회피할 목적으로 ECMAScript/Javascript 코드에서 발견된 패턴을 개발자에게 알려주는 플러그인이다.
- `browerserslist`: 전 세계 사용 통계 속에서 상위 1% 이상 사용된 브라우저, 각 브라우저의 최신 버전 2개를 지원하도록 한다.


---

#### Reference

- Vue.js 프로젝트 투입 일주일 전 | 고승원 지음