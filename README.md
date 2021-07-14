# Webpack

https://webpack.js.org/

매우 `꼼꼼한` 구성으로 중/대형 프로젝트에 적합하다.

## 프로젝트 생성

1. package.json을 생성한다.

```
npm init -y
```

2. webpack을 설치한다.

```
npm i -D webpack webpack-cli webpack-dev-server@next
```

webpack-cli와 webpack-dev-server의 메이저 버전을 일치시켜야 하므로 `@next`를 붙여 패키지 설치를 진행한다.

3. package.json에 명령어를 추가한다.

```json
{
  "scripts": {
    "dev": "webpack-dev-server --mode development",
    "build": "webpack --mode production"
  }
}
```

4. webpack.config.js 파일을 만든다.

## entry, output

parcel bundler는 자동으로 옵션을 제공해주며 구성옵션을 제공하지 않는다. 반면에, webpack은 직접 구성옵션을 제공하여 정리해야 한다. 구성옵션을 작성해주어야 한다는 단점이 있지만, 디테일한 옵션설정을 할 수 있다.

```js
module.exports = {
  entry: "./js/main.js",
};
```

`entry`는 파일을 읽어들이기 시작하는 진입점을 설정하는 옵션이다. parcel bundler에서는 parcel index.html 이라는 CLI를 통해 진입점을 설정했었다. 반면에 webpack은 .webpack.config.js에서 진입점을 설정해야 한다. parcel은 html 파일을 진입점으로 사용했지만 webpack은 `JS파일`을 진입점으로 사용한다. entry 에는 읽어올 자바스크립트의 경로를 지정한다.

`output`은 entry에서 읽어온 파일의 기본적인 연결관계를 분석하여 결과물(bundle)을 반환하는 설정이다. ouput에는 `객체` 데이터를 통해 내용을 추가할 수 있다. 대표적인 옵션으로는 `path`와 `filename`이 있다. `path`에는 어떤 폴더에다가 결과물을 내어줄 것인지 설정할 수 있고, 그리고 그 결과를 내어줄 파일 이름을 `filename`에 적을 수 있다.

```js
const path = require("path");

module.exports = {
  entry: "./js/main.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "main.js",
  },
};
```

output에서 제공하는 `path`라는 옵션은 상대경로가 아니라 node.js에서 요구하는 `절대경로`로 작성되어야 한다. node.js에서 사용할 수 있는 전역모듈을 가져와 변수에 할당하여 output에서 사용한다. path라는 변수는 resolve를 사용할 수 있다. `resolve`는 첫번째, 두번째 인수를 기본적인 경로를 합쳐준다. `__dirname`도 node.js에서 사용할 수 있는 전역 모듈로 현재 파일이 있는 경로를 지칭한다. 그래서 resolve를 통해 객체 path에 절대경로를 전해줄 수 있다.

이제 dist라는 폴더에 main.js라는 파일이름으로 entry에서 진입점으로 사용한 경로의 모든 파일들을 번들로 만들어 합쳐준다. 이제 CLI로 npm run build를 입력하면 dist폴더에 main.js가 번들된 것을 확인할 수 있다.

```js
const path = require("path");

module.exports = {
  entry: "./js/main.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "main.js",
    clean: true,
  },
};
```

ouput의 파일네임을 변경하고 다시 빌드하면 다른 이름의 똑같은 파일이 번들된다. 이를 막기 위해서는 `clean`을 사용하면 된다. clean을 사용하면 새롭게 빌드하였을 때, 기존의 필요하지 않은 내용들을 깨끗하게 제거해준다.

```js
const path = require("path");

module.exports = {
  entry: "./js/main.js",
  output: {
    // path: path.resolve(__dirname, "dist"),
    // filename: "main.js",
    clean: true,
  },
};
```

path는 따로 경로를 입력해주지 않아도 dist라는 폴더에 번들된다. filename은 entry에서 지정한 진입점 파일의 이름으로 생성된다. 그래서 두 옵션은 따로 설정하지 않아도 괜찮다. 만약 구성이 복잡해진다면 path와 filename을 사용하는 것이 좋다.

## plugins

index.html을 이용해서 개발서버를 오픈하여 브라우저에서 내용을 확인할 수 있도록 설정해보자. webpack.config.js를 통해 main.js를 진입점으로 설정했다. 패키지를 통해 main.js에서 index.html을 삽입하여 개발서버로 오픈할 수 있다.

1. html-webpack-plugin 패키지를 설치한다.

```
npm i -D html-webpack-plugin
```

2. 설치한 패키지를 webpack.config.js에 불러온다.

```js
const HtmlPlugin = require("html-webpack-plugin");
```

3. plugins 구성옵션을 추가한다.

```js
module.exports = {
  //...

  plugins: [],
};
```

> plugins는 번들링 후 결과물의 처리 방식 등 다양한 플러그인들을 설정한다.

4. HtmlPlugin 생성자를 만들고 template에 경로를 지정한다.

```js
module.exports = {
  //...

  plugins: [
    new HtmlPlugin({
      template: "./index.html",
    }),
  ],
};
```

5. npm run dev를 통해 개발서버를 연다.

만약 개발서버를 열었는데 localhost 대신 다른 문자가 입력되어있다면, `devserver` 구성옵션을 추가하면 된다.

```js
module.exports = {
  //...

  devServer: {
    host: "localhost",
  },
};
```

## 정적 파일 연결

index.html에 image를 이용해도 webpack에서는 바로 적용되지 않는다. webpack의 구성옵션을 추가하여 이미지를 번들할 수 있다.

1. 패키지를 설치한다.

```
npm i -D copy-webpack-plugin
```

2. 해당 패키지를 webpack.config.js파일에 불러온다.

```js
const CopyPlugin = require("copy-webpack-plugin");
```

3. plugins에서 CopyPlugin을 생성한다.

```js
module.exports = {
  //..

  plugins: [
    //..
    new CopyPlugin({}),
  ],
};
```

4. patterns에 from을 사용하여 이미지가 들어간 폴더이름을 입력한다.

```js
module.exports = {
  //..

  plugins: [
    //..
    new CopyPlugin({
      patterns: [
        {
          from: "static",
        },
      ],
    }),
  ],
};
```

5. html파일에 이미지 경로를 입력한다.

```html
<img src="./images/sample.png" alt="로고" />
```

> 실제 이미지 파일의 경로는 ./static/images/sample.png 이지만 dist 폴더에 번들되었을 때, dist 폴더 주변에서 images 폴더를 찾을 수 있다.

## module

static 폴더에 모든 폴더와 파일들을 넣을 필요없이 자바스크립트의 import를 이용해서도 번들할 수 있다.

```js
import "../css/main.css";
```

webpack 파일은 css파일을 읽을 수 없어 문제가 발생할 수 있다. 단순히 파일을 합쳐서 dist라는 폴더에 번들할 뿐이다. 그래서 CSS파일을 읽을 수 있는 패키지를 설치할 수 있다.

1. CSS 패키지를 설치한다.

```
npm i -D css-loader style-loader
```

> css-loader는 자바스크립트에서 css파일을 해석할 수 있도록 하며, style-loader는 해석된 내용을 index.html에 삽입하는 역할을 한다.

2. webpack.config.js에 해당 패키지를 명시한다.

```js
module.exports = {
  //..
  module: {
    rules: [
      {
        // 정규표현식
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

> 반드시 순서는 style-loader 부터 작성해야 한다.

## SCSS

개발을 할 때, css보다는 scss의 사용이 선호된다. scss를 연결해보자. 기존의 css 파일들을 모두 scss로 바꾼다.

```js
import "../scss/main.scss";
```

```js
module.exports = {
  //..
  module: {
    rules: [
      {
        // 정규표현식
        test: /\.s?css$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

1. SCSS를 해석할 수 있는 패키지를 설치한다.

```
npm i -D sass-loader sass
```

> sass-loader는 파일을 읽어오고 sass는 해당 파일을 번역해준다.

2. sass-loader를 추가한다.

```js
module.exports = {
  //..
  module: {
    rules: [
      {
        // 정규표현식
        test: /\.css$/,
        use: ["style-loader", "css-loader", "sass-loader"],
      },
    ],
  },
};
```

## Autoprefixer(PostCSS)

공급업체접두사를 자동으로 붙여주려면 Autoprefixer가 필요하다.

1. 패키지 설치

```
npm i -D postcss autoprefixer postcss-loader
```

2. postcss-loader를 추가한다.

```js
module.exports = {
  //..
  module: {
    rules: [
      {
        // 정규표현식
        test: /\.css$/,
        use: ["style-loader", "css-loader", "postcss-loader", "sass-loader"],
      },
    ],
  },
};
```

3. package.json에 browserslist를 추가한다.

```json
{
  "browserslist": [">1%", "last 2 versions"]
}
```

4. .postcssrc.js 파일을 생성하여 구성옵션을 설정한다.

```js
module.exports = {
  plugins: [require("autoprefixer")],
};
```

## babel

1. 패지키를 설치한다.

```
npm i -D @babel/core @babel/preset-env @babel/plugin-transform-runtime
```

2. .babelrc.js를 생성하여 구성옵션을 설정한다.

```js
module.exports = {
  presets: ["@babel/preset-env"],
  plugins: [["@babel/plugin-transform-runtime"]],
};
```

3. webpack.config.js module에 옵션을 추가한다.

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ["babel-loader"],
      },
    ],
  },
};
```

4. babel-loader를 설치한다.

## NPX, Degit

parcel이나 webpack 패키지를 설치하는 일을 좀 더 쉽게 할 수 있는 CLI가 있다. 기본적인 번들러를 따로 만들어두고 새로운 프로젝트에 넣을 수 있다.

1. 패키지를 설치한다.

```
npx degit githubId/webpack-template-basic folderName
```

2. 생성한 폴더로 접근한다.

```
cd folderName
```

3. 접근한 폴더를 VScode로 연다.

```
code . -r
```
