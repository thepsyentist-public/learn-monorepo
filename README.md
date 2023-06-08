# 모노레포: 개념과 활용

<br />

## 세미나 자료
https://bit.ly/3WI2zsC  
<br />

## 시작하기
```bash
$ git clone https://github.com/thepsyentist-public/learn-monorepo
```
```bash
$ yarn set version berry
```
```bash
$ yarn
```
```bash
$ yarn workspace @thepsyentist/client dev
```
<br />

## 모노레포 프로젝트 설정하기 with Yarn berry

`learn-monorepo` 프로젝트를 구성하고 있는 내용으로 작성했습니다.

### Step1. 개발 환경 버전 설정

모노레포를 구성하기 위해서는 `Node.js` `yarn` 버전 설정이 필요합니다.  
https://yarnpkg.com/getting-started/install

Node.js 버전 매니징이 필요하다면 nvm 을 설치해서 관리할 수 있습니다.  
https://jang8584.tistory.com/295

> Node.js: 16.10.0 버전 이상  
> yarn: berry

<br />
<hr />
<br />

### Step2. yarn workspace 프로젝트 만들기

`프로젝트 폴더 생성`: learn-monorepo 폴더를 생성합니다.  
```bash
$ mkdir learn-monorepo
$ cd learn-monorepo
```
<br />

`yarn 버전 설정`: yarn berry 버전으로 설정합니다.  
```bash
$ yarn set version berry
```
<br />

`workspace 패키지 설정`: 모노레포 구성을 위해 yarn workspace 프로젝트로 설정합니다.  
- 추가적인 [yarn berry cli](https://yarnpkg.com/cli/init)를 확인해보세요.   
```bash
$ yarn init -w
```
<br />

`{rootDirectory} package.json 파일 수정`: workspaces 속성에서 관리를 할 workspace 폴더 경로를 등록합니다.  
```json
// package.json
{
  "name": "learn-monorepo",
  "packageManager": "yarn@3.5.0",
  "private": true,
  "workspaces": [
    "apps/*",       // 👈 추가
    "packages/*"    // 👈 추가
  ]
}
```
위와 같이 설정하면 `apps/*` `packages/*` 내부 폴더는 워크스페이스로 활용 가능합니다.  
<br />

`apps 폴더에 Nextjs 프로젝트 추가`: Nextjs CRA를 통해 프로젝트를 생성합니다.  
```bash
$ cd apps
$ yarn create next-app
```
<img width="600" src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/9248160f-ed14-4f49-80aa-cddaf1285606" alt="next cra" />  

`client` 프로젝트를 생성했습니다.  
이 프로젝트에 다른 패키지들을 추가하는 형식으로 진행하겠습니다.  
<br />

`workspace 이름 설정`: 프로젝트의 워크스페이스 이름을 설정합니다. **apps/client/package.json** 파일 내 name을 변경합니다.  
<img width="600" src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/02fdd13e-9a8e-412f-98c1-23f6dee7ad7a" alt="workspace name" />  
<br />

`workspace 적용`: 프로젝트를 생성하면 .yarnrc.yml에 프로젝트의 종속성을 인지하도록 추가해야합니다.  
```bash
// {rootDirector} 로 이동

$ ..cd
$ yarn
```
**yarn** 명령어를 통해서 workspaces에 등록된 프로젝트들을 인덱싱하여 **.yarnrc.yml**에 종속성을 추가합니다.  
<img width="400" src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/b52fbb16-ec12-4758-a335-8f1e56cd1758" alt="yarnrc" />  

<br />

`@thepsyentist/client 프로젝트 실행`: 지금까지 설정한 프로젝트를 실행 해봅니다.  
```bash
$ yarn workspace @thepsyentist/client dev
```
<br />
<hr />
<br />

### Step3. 프로젝트에 타입스크립트 적용하기

지금까지의 설정으로는 `./apps/client/pages/index.tsx` 파일의 코드를 확인하면 typescript error가 발생합니다.<br /> 
오류가 발생하는 이유는, yarn berry pnp는 `node_modules` 모듈을 불러오는 방식이 달라서 발생하는 문제입니다.
<br />

`typescript 설치`: {rootDirectory}에 typescript 의존성을 설치합니다.  
```bash
$ yarn add -D typescript

// vscode의 경우는 아래 cli도 설치합니다.
$ yarn dlx @yarnpkg/sdks vscode
```
<br />

`Node.js 패키지 매니저 설정`: 웹스톰의 경우 **settings → Nodejs 검색 → 패키지 매니저에서 Yarn berry로 지정** 을 설정합니다.
<img width="800" src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/7bce3656-55d3-4e9e-9cc2-ead56fac782d" alt="패키지 매니저" />

`vscode extension 설치`: vscode IDE를 사용하는 경우, **arcanis.vscode-zipfs** extends를 설치합니다.  
```json
// .vscode/extensions.json

{
  "recommendations": ["arcanis.vscode-zipfs"]
}
```  

.vscode/extensions.json 에 내용을 추가하면, 다른 사용자가 프로젝트 이용시 extensions를 설치를 추천하도록 설정 할 수 있습니다.  

<br />
<hr />
<br />

### Step4. 공통 라이브러리 패키지 만들고 적용하기

`packages/lib` 에 공통으로 사용할 패키지를 만들어서 적용해봅니다.  
<br />

`packages/lib 생성`: lib 패키지를 생성합니다.  
```bash
$ cd packages/lib
$ yarn init
$ yarn add typescript // 타입스크립트 의존성 추가
```
<br />

`workspace 이름 설정`: **packages/lib/package.json** 내 name을 `@thepsyentist/lib` 로 변경합니다.
```json
// packages/lib/package.json

{
  "name": "@thepsyentist/lib",
  "main": "./src/index.ts",
  "packageManager": "yarn@3.6.0",
  "dependencies": {
    "typescript": "^5.1.3"
  }
}

```
<br />

`tsconfig.json 추가`: lib 패키지에 tsconfig.json 파일을 생성 후 내용을 추가합니다.  
```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "compilerOptions": {
    "strict": true,
    "useUnknownInCatchVariables": true,
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "newLine": "lf",
    "module": "ESNext",
    "moduleResolution": "node",
    "target": "ESNext",
    "lib": ["ESNext", "dom"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "noEmit": false,
    "incremental": true,
    "resolveJsonModule": true,
    "paths": {}
  },
  "exclude": ["**/node_modules", "**/.*/", "./dist", "./coverage"],
  "include": ["**/*.ts", "**/*.js", "**/.cjs", "**/*.mjs", "**/*.json"]
}
```
<br />

`공유 함수 추가`: packages/lib/src/index.js 파일 생성 후, 아래 코드 작성합니다.  
```ts
export const sayHello = () => {
   return "Hello World";
};
```  
<br />

`워크스페이스 등록`: {rootDirectory}로 이동해서 프로젝트에 의존성을 추가합니다.
```bash
$ cd ..
$ cd ..

// 현재 경로 {rootDirectory}
$ yarn
```

여기까지 `packages/lib` 의 공통 라이브러리를 만들었습니다.  

<br />
<hr />
<br />

### Step5. client 프로젝트에 라이브러리 패키지 사용하기

`설치`: Step4에서 생성한 `packages/lib` 를 `apps/client` 프로젝트에 의존성으로 추가해서 활용해봅니다.  
```bash
$ yarn workspace @thepsyentist/client add @thepsyentist/lib
```
<img width="300" src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/60b02fe5-9316-4534-8d22-9b1034aea48f" alt="라이브러리 패키지 사용" />   
<br />
<br />

`Nextjs 설정`: Nextjs에서는 외부 패키지를 의존성을 추가할 경우, 해당 패키지를 트랜스파일 옵션을 적용할 수 있습니다.  
```json
// apps/client/next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  transpilePackages: ['@thepsyentist/lib'],
};

module.exports = nextConfig;
```
<br />

`적용`: apps/client/src/app/page.tsx 파일 내 sayHello 함수를 호출해서 적용합니다.  
<img width="400" src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/683c5dfb-2926-4d7f-b81e-751942c36fb7" alt="함수호출" />  
<br />

`실행`: @thepsyentist/client 프로젝트를 실행하여 내용이 반영되었는지 확인합니다.  
```bash
$ yarn workspace @thepsyentist/client dev
```
<br />
<br />

## 모노레포 패키지 활용하기

모노레포를 통해 활용할 수 있는, 설정 적용 및 공유, 공통 UI 활용, 스크립트 활용 내용을 학습합니다.  

### Step1. 공통 tsconfig 적용해보기

`tsconfig.base.json 생성`: 워크스페이스 마다 매번 tsconfig 설정을 하는것을 번거로울 작업입니다. {rootDirectory}에 워크스페이스에 공유 할 수 있는 파일을 생성합니다.
```json
// tsconfig.base.json

{
  "compilerOptions": {
    "strict": true,
    "useUnknownInCatchVariables": true,
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "incremental": true,
    "newLine": "lf"
  },
  "exclude": ["**/node_modules", "**/.*/"]
}
```
<br />

`client, lib에 적용`: tsconfig 설정 중 extends 항목을 통해 tsconfig.base.json를 적용합니다.  
```json
// apps/client/tsconfig.json

{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "../../tsconfig.base.json",     // 👈 추가
  "compilerOptions": {
    "baseUrl": "./src",
    "target": "esnext",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "jsx": "preserve",
    "incremental": true,
    "paths": {
      "@/*": ["./*"]
    }
  },
  "exclude": ["**/node_modules", "**/.*/"],
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    "**/*.mts",
    "**/*.js",
    "**/*.cjs",
    "**/*.mjs",
    "**/*.jsx",
    "**/*.json"
  ]
}
```
```json
// packages/lib/tsconfig.json

{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "../../tsconfig.base.json",     // 👈 추가
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "node",
    "target": "ESNext",
    "lib": ["ESNext", "dom"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "noEmit": false,
    "incremental": true,
    "resolveJsonModule": true
  },
  "exclude": ["**/node_modules", "**/.*/", "./dist", "./coverage"],
  "include": ["**/*.ts", "**/*.js", "**/.cjs", "**/*.mjs", "**/*.json"]
}
```
위 와 같이 extends에 tsconfig.base.json을 추가하여 타입스크립트 설정을 공유할 수 있습니다.

<br />
<hr />
<br />

### Step2. eslint, prettier 적용하기

`의존성 설치`: 아래 CLI를 실행하여 eslint, prettier 관련 의존성을 {rootDirectory}에서 설치합니다.  
```bash
$ yarn add prettier eslint eslint-config-prettier eslint-plugin-import eslint-plugin-react eslint-plugin-react-hooks eslint-import-resolver-typescript @typescript-eslint/eslint-plugin @typescript-eslint/parser -D
```

`플러그인 설치`: IDE의 플러그인에서 eslint, prettier를 검색하여 설치합니다.  
<br />

`eslint 설정 파일 추가`: {rootDirectory}에서 .eslintrc.js 파일을 생성합니다.
```js
module.exports = {
  root: true,

  env: {
    es6: true,
    node: true,
    browser: true,
  },

  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: { jsx: true },
  },

  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier',
  ],
  plugins: ['@typescript-eslint', 'import', 'react', 'react-hooks'],
  settings: {
    'import/resolver': { typescript: {} },
    react: { version: 'detect' },
  },
  rules: {
    'no-implicit-coercion': 'error',
    'no-warning-comments': [
      'warn',
      {
        terms: ['TODO', 'FIXME', 'XXX', 'BUG'],
        location: 'anywhere',
      },
    ],
    curly: ['error', 'all'],
    eqeqeq: ['error', 'always', { null: 'ignore' }],

    // Hoisting을 전략적으로 사용한 경우가 많아서
    '@typescript-eslint/no-use-before-define': 'off',
    // 모델 정의 부분에서 class와 interface를 합치기 위해 사용하는 용법도 잡고 있어서
    '@typescript-eslint/no-empty-interface': 'off',
    // 모델 정의 부분에서 파라미터 프로퍼티를 잘 쓰고 있어서
    '@typescript-eslint/explicit-function-return-type': 'off',
    '@typescript-eslint/no-parameter-properties': 'off',
    '@typescript-eslint/no-var-requires': 'warn',
    '@typescript-eslint/no-non-null-asserted-optional-chain': 'warn',
    '@typescript-eslint/no-inferrable-types': 'warn',
    '@typescript-eslint/no-empty-function': 'off',
    '@typescript-eslint/naming-convention': [
      'error',
      {
        format: ['camelCase', 'UPPER_CASE', 'PascalCase'],
        selector: 'variable',
        leadingUnderscore: 'allow',
      },
      { format: ['camelCase', 'PascalCase'], selector: 'function' },
      { format: ['PascalCase'], selector: 'interface' },
      { format: ['PascalCase'], selector: 'typeAlias' },
    ],
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    '@typescript-eslint/array-type': ['error', { default: 'array-simple' }],
    '@typescript-eslint/no-unused-vars': ['error', { ignoreRestSiblings: true }],
    '@typescript-eslint/member-ordering': [
      'error',
      {
        default: [
          'public-static-field',
          'private-static-field',
          'public-instance-field',
          'private-instance-field',
          'public-constructor',
          'private-constructor',
          'public-instance-method',
          'private-instance-method',
        ],
      },
    ],

    'import/order': [
      'error',
      {
        groups: ['builtin', 'external', 'internal', 'parent', 'sibling', 'index', 'object'],
        alphabetize: { order: 'asc', caseInsensitive: true },
      },
    ],

    'react/prop-types': 'off',
    // React.memo, React.forwardRef에서 사용하는 경우도 막고 있어서
    'react/display-name': 'off',
    'react-hooks/exhaustive-deps': 'error',
    'react/react-in-jsx-scope': 'off',
    'react/no-unknown-property': ['error', { ignore: ['css'] }],
  },
};
```
<br />

`eslint 설정 등록`: IDE에 eslint가 활성화 되도록 설정을 등록합니다.  
- Webstorm 은 **settings → eslint → Automatic Eslint configuration** 을 지정합니다.  
<img src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/7113fd01-59b7-4fbe-8fad-ba8715bbf1c3" width="600" alt="eslint 설정" />
<br />
<br />
- vscode 는 .vscode/setting.json 에 설정을 추가합니다.

<br />

```json
{
  "search.exclude": {
    "**/.yarn": true,
    "**/.pnp.*": true
  },
  "typescript.tsdk": ".yarn/sdks/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "eslint.nodePath": ".yarn/sdks",
  "prettier.prettierPath": ".yarn/sdks/prettier/index.js",

  // 기본 포맷터 prettier로 사용
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  // 파일 저장시 formatter 실행
  "editor.formatOnSave": true,
  "editor.rulers": [120],

  // 추가되는 내용
  "eslint.packageManager": "yarn",
  "eslint.validate": ["javascript", "javascriptreact", "typescript", "typescriptreact"]
}
```

<br />

`eslint 패키지로 구성해서 공유하고 싶다면?`: [https://tech.kakao.com/2019/12/05/make-better-use-of-eslint](https://tech.kakao.com/2019/12/05/make-better-use-of-eslint)를 참고해서 구성해볼수 있습니다.  
<br />

`.prettierrc 파일 생성`: {rootDirectory}에서 .prettierrc 파일을 생성합니다.

```json
{
  "arrowParens": "avoid",
  "bracketSameLine": false,
  "bracketSpacing": true,
  "endOfLine": "lf",
  "jsxSingleQuote": false,
  "printWidth": 120,
  "proseWrap": "preserve",
  "quoteProps": "as-needed",
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

<br />

`prettier 설정 등록`: IDE에 prettier가 활성화 되도록 설정을 등록합니다.  
- Webstorm은 **settings → prettier 검색 → Automatic Prettier configuration**을 지정합니다.  
<img src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/070f744d-f4cf-44fb-8931-9f5696acdf2f" width="400" alt="prettier" />  
<br />
- vscode는 `.vscode/settings.json` 내용을 추가합니다.


```json
{
  "search.exclude": {
    "**/.yarn": true,
    "**/.pnp.*": true
  },
  "typescript.tsdk": ".yarn/sdks/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "eslint.nodePath": ".yarn/sdks",
  "prettier.prettierPath": ".yarn/sdks/prettier/index.js",

  // 기본 포맷터 prettier로 사용
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  // 파일 저장시 formatter 실행
  "editor.formatOnSave": true,
  "editor.rulers": [120]
}
```

<br />
<hr />
<br />

### Step3. 리액트 컴포넌트 공유하기  

`프로젝트 생성`: packages/ui 폴더를 생성하고, 프로젝트를 구성합니다.  
```bash
$ cd packages/ui
$ yarn init
```
<br />

`워크스페이스 이름 변경`: packages/ui/package.json 파일 내 name을 변경합니다.  
```json
{
  "name": "@thepsyentist/ui",
  ...
}
```
<br />

`리액트 의존성 설치`: 아래 cli를 {rootDirectory}로 이동하여 의존성을 설치합니다.  
```json
// root폴더로 이동
$ cd ../../

// 갱신
$ yarn


// install
$ yarn workspace @wanted/ui add typescript react react-dom @types/node @types/react @types/react-dom -D
```
<br />

`tsconfig 추가`: packages/ui/tsconfig.js 파일을 생성하여, 아래 내용을 추가합니다.  
```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "baseUrl": "./src",
    "target": "esnext",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "jsx": "react-jsx",
    "noEmit": false,
    "incremental": true
  },
  "exclude": ["**/node_modules", "**/.*/", "dist", "build"]
}
```
<br />

`Button UI 추가`: 공유 할 버튼 컴포넌트를 추가합니다.  
```tsx
// packages/ui/src/Button.tsx

import { ButtonHTMLAttributes, MouseEventHandler, ReactNode } from 'react';

export type ButtonProps = ButtonHTMLAttributes<HTMLButtonElement> & {
  children: ReactNode,
  onClick?: MouseEventHandler<HTMLButtonElement>,
};

const Button = (props: ButtonProps) => {
  const { children, onClick, ...other } = props;

  return (
    <button type="button" onClick={onClick} {...other}>
      {children}
    </button>
  );
};

export default Button;
```
```ts
// packages/ui/src/index.ts

export { default as Button } from './Button';
```
<br />

`main 등록`: package.json에 main 속성을 추가합니다. main을 지정하여 다른 워크스페이스에서 사용할 경우, 참조하도록 합니다.
```json
{
  "name": "@thepsyentist/ui",
  "packageManager": "yarn@3.5.0",
  "main": "src/index.ts",    // 👈 추가
  "devDependencies": {
    "@types/node": "^18.16.3",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.1",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "typescript": "^5.0.4"
  }
}
```
<br />

`client에서 Button 활용하기`: {rootDirectory}에서 pakcages/ui 의존성을 추가합니다.
```bash
$ cd ..
$ cd ..
$ yarn workspace @thepsyentist/client add @thepsyentist/ui
```
<br />

`Next.js config 수정`: transpilePackages에 추가합니다.  
```json
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  transpilePackages: ['@thepsyentist/lib', '@thepsyentist/ui'],
};

module.exports = nextConfig;
```
<br />

`페이지에 Button 추가하기`: apps/client/pages/index.tsx 파일에 버튼을 추가합니다.  
<img src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/5e78959e-e2e6-41b8-a290-4503cdc5132e" width="500" alt="버튼 추가" />
<br />

`프로젝트 실행`: 페이지에 버튼이 추가되었는지 프로젝트를 실행합니다.  
```bash
$ yarn workspace @thepsyentist/client dev
```
<br />
<hr />
<br />

### Step4. type check 스크립트 병렬 적용하기

여러 워크스페이스마다 스크립트를 병렬로 실행하는 방법을 알아보겠습니다.  
등록 된 워크스페이스마다 lint, type check 등 스크립트를 실행하여 체크를 할 수 있습니다.  

가장 간단한 방법으로는 각 워크스페이스 디렉토리에 접근하여 명령어로 실행 할 수있습니다.  
하지만 여러 워크스페이스를 관리하는 모노레포 구성에서는 매우 비효율적인 방식입니다.  
이것을 활용하기위해 `yarn plugin`을 활용해봅니다.
<br />

`workspace-tools plugin 설치`: 아래 CLI를 입력하여 workspace-tools를 설치합니다.  
```bash
$ yarn plugin import workspace-tools
```

`typecheck script 추가`: 아래 각 워크스페이스에 type check script를 추가합니다.  
- apps/client/package.json
- packages/lib/package.json
- packages/ui/package.json

```json
"scripts": {
  "typecheck": "tsc --project ./tsconfig.json --noEmit"
},
```

`g:typecheck 스크립트 추가`: {rootDirectory}에서 스크립트 실행을 위해 package.json에 script를 추가합니다.  
```json
"scripts": {
  "g:typecheck": "yarn workspaces foreach -pv run typecheck"
},
```
> `yarn workspaces foreach`명령어 option 확인  
>`p`: 병렬 실행  
>`v`: workspace name 출력  

<br />

`타입체크 스크립트 실행`: {rootDirectory}에서 워크스페이스의 타입체크 스크립트를 실행합니다.  
<img src="https://github.com/thepsyentist-public/learn-monorepo/assets/39726717/901a5d21-981e-4c23-ad63-596244429c0a" width="600" alt="타입체크" />  

위 내용처럼 각 워크스페이스에서 원하는 스크립트를 등록하고,  
{rootDirectory} package.json에 스크립트를 등록하여 모든 워크스페이스를 관리할 수 있습니다.  
