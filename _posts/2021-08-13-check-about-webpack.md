---
layout: post
title: "웹팩 톺아보기"
author: ppamppamman
date: "2021-08-13"
categories: [Dev, Webpack]
image: https://user-images.githubusercontent.com/13144573/130372750-d2d48a04-0df8-4126-b1fc-93576b90676a.png
image_desc: '다들 준비 됐나? require("./dist/네_선장님.bundle.js");'
---
라떼는 말이야.. node.js를 처음 공부했을 때 gulp나 grunt를 다루게 했었다지..

- Node.js 환경을 활용한 모듈화 도구
    - 모듈 : AMD, CommonJS ...
    - 우리가 일반적으로 쓰는 웹팩의 명세는 CommonJS 방식

    ```jsx
    module.exports = {message: 'webpack'};
    alert(require('./examplemodule).message);
    // ==> "webpack"
    ```

    ```jsx
    // hello.js
    module.exports = 'Hello';

    // world.js
    module.exports = 'World';

    // greeting.js
    var greeting = require('./hello') + require('./world');
    module.exports = greeting;

    // ======
    alert(require('./greeting'));
    // ==> "Hello World"
    ```

- 이렇게 되면 greeting은 전역변수로 선언이 되었다고 이야기할 수 있다.
- 다만 webpack으로 모듈을 컴파일한 경우 지역변수 처리된다.

    ```json
    ...
    /* 1 */
    /***/ function(module, exports, __webpack_require__) {
        var greeting = __webpack_require__(2) + __webpack_require__(3);
        module.exports = greeting;

        // __webpack_require__ 함수 -> 숫자 parameter

    /***/ },
    ...
    ```

- 실습

    `npm install webpack -g`

    - index.js 작성

    `webpack ./index.js`

    ![https://user-images.githubusercontent.com/13144573/129313393-a8a6f030-4ada-4dad-b59f-cbe35f68391f.png](https://user-images.githubusercontent.com/13144573/129313393-a8a6f030-4ada-4dad-b59f-cbe35f68391f.png)

    cf) Windows의 경우 `sudo` 권한 없어서 에러 발생

    `sudo npm install webpack-cli -g`

    ![https://user-images.githubusercontent.com/13144573/129313396-15b2bc4b-85a4-4738-920b-7141639a3bcd.png](https://user-images.githubusercontent.com/13144573/129313396-15b2bc4b-85a4-4738-920b-7141639a3bcd.png)

    ![https://user-images.githubusercontent.com/13144573/129313401-de2c8f12-da17-45fd-ae68-e65090d6be0c.png](https://user-images.githubusercontent.com/13144573/129313401-de2c8f12-da17-45fd-ae68-e65090d6be0c.png)

    - 매번 webpack 커맨드의 파라미터를 지정해줘야 할까 ?
        - 설정 파일 작업

        ```jsx
        //webpack.config.js
        module.exports = {  
        	context: __dirname + '/app', // 모듈 파일 폴더 
        	entry: { // 엔트리 파일 목록 
        		app: './app.js'  
        	}, 
        	output: { 
        		path: __dirname + '/dist', // 번들 파일 폴더 
        		filename: '[name].bundle.js' // 번들 파일 이름 규칙 
        	}
        }
        ```

        ![https://user-images.githubusercontent.com/13144573/129313402-0a208799-99f5-4c00-a074-166fc7dd3f52.png](https://user-images.githubusercontent.com/13144573/129313402-0a208799-99f5-4c00-a074-166fc7dd3f52.png)

        ![https://user-images.githubusercontent.com/13144573/129313404-09e2b459-2441-4353-abcf-d377a0bfb385.png](https://user-images.githubusercontent.com/13144573/129313404-09e2b459-2441-4353-abcf-d377a0bfb385.png)

- 왜 난독화가 되었을까?
    - 기본값이 프로덕션 빌드에 맥시멈 퍼포먼스를 내기 위해 갈아버리는 값으로 설정되어 있어서이다.
    - devtool: sourcemap 관련 내용으로 체크
        - [https://webpack.js.org/configuration/devtool/#devtool](https://webpack.js.org/configuration/devtool/#devtool)
    - eval-source-map

        ![https://user-images.githubusercontent.com/13144573/129313406-90aa09ce-5925-4b75-a36f-2ca9e40a39e2.png](https://user-images.githubusercontent.com/13144573/129313406-90aa09ce-5925-4b75-a36f-2ca9e40a39e2.png)

## 좋습니다. 와카리마시따 센세. 그래서 저희는 이걸로 뭘 어떻게 해야하죠

- 좋은 질문이다.

    ![https://user-images.githubusercontent.com/13144573/129313408-0c5cc8c2-5861-49d2-9aa5-d4a27c96b456.png](https://user-images.githubusercontent.com/13144573/129313408-0c5cc8c2-5861-49d2-9aa5-d4a27c96b456.png)

- 뭘 할 것인지, 어떻게 할 것인지 이전에 우리가 물어봐야 할 것은 **"이것을 왜 써야하는지?**" 란다.
    - 웹팩은 필요성에 대해 이렇게 이야기하고 있단다.
        - 파일 단위의 자바스크립트 모듈 관리의 필요성
            - 문법 수준에서 모듈을 지원하기 시작한 것은 ES2015부터다.
            - import/export 구문이 없었던 모듈 이전 상황을 살펴보는 것이 웹팩 등장 배경을 설명하는데 수월할 것 같다.
            - 이고잉 센세 : [https://www.youtube.com/watch?v=1V6XiP16D28](https://www.youtube.com/watch?v=1V6XiP16D28)
        - 웹 개발 작업 자동화 도구 (Web Task Manager) - 캡틴 판교 센세

            > 이전부터 프런트엔드 개발 업무를 할 때 가장 많이 반복하는 작업은 텍스트 편집기에서 코드를 수정하고 저장한 뒤 브라우저에서 새로 고침을 누르는 것이었습니다. 그래야 화면에 변경된 내용을 볼 수 있었죠.

            > 이외에도 웹 서비스를 개발하고 웹 서버에 배포할 때 아래와 같은 작업들을 해야 했습니다. HTML, CSS, JS 압축이미지 압축CSS 전처리기 변환

            > 이러한 일들을 자동화 해주는 도구들이 필요했습니다. 그래서 Grunt와 Gulp 같은 도구들이 등장했습니다.

        - 웹 애플리케이션의 빠른 로딩 속도와 높은 성능
- 정리
    - 왜 ?
        - 모듈관리
        - 빌드 작업 자동화
        - 성능
---

## 왜 웹팩은 주류가 되었나 ?
- 주류라는 것은 주류가 되지 못한 것들이 있기 마련이다.
    - 비슷한 역할을 하는 것들에는 parcel gulp grunt 등이 있다.
        - gulp, grunt : 태스크 정의
        - parcel : 웹팩과 비슷하지만 웹팩보다 훨씬 쉬운 빌드툴
    - 의존성 관리에 필요하다.
    - gulp vs webpack : [https://kdydesign.github.io/2017/07/27/webpack/](https://kdydesign.github.io/2017/07/27/webpack/)
    - 요약 : [https://github.com/ipadorusa/sri-vuejs/wiki/Grunt-or-Gulp-vs-Webpack-%EC%9D%98-%EC%B0%A8%EC%9D%B4](https://github.com/ipadorusa/sri-vuejs/wiki/Grunt-or-Gulp-vs-Webpack-%EC%9D%98-%EC%B0%A8%EC%9D%B4)

### 모듈 관리

- 위에서 봤다.

### 빌드 작업 자동화

- loader
    - 웹팩은 모든 파일을 모듈로 바라본다.
    - 자바스크립트로 만든 모듈은 당연.
    - 스타일시트, 이미지, 폰트까지도 전부 모듈로 보기 때문에 import 구문을 사용하면 자바스크립트 코드 안으로 가져올수 있다.

    **[종류]**
    - css-loader
        - css를 import 해서 쓰는 것을 모듈라이징하는 경우
    - style-loader
        - css코드가 js로 변경되었으나 이것이 DOM에는 올라가지 않은 것. js로 변경된 스타일을 동적으로 DOM에 추가하는 로더
        - 따라서 css-loader와 style-loader는 같이 가야한다.
    - sass-loader
        - sass파일을 node-sass를 통해 css로 컴파일하기 위해 필요
    - file-loader
        - 소스코드에서 사용하는 모든 파일 (aka .font, .png, .jpeg ... )을 아웃풋으로 처리
    - url-loader
        - 네트워크 리소스를 줄이기 위해 (GET 요청을 줄이기위해) 이미지 같은 파일을 base64로 인코딩하여 처리하고자 하는 경우
- plugins
    - 번들된 결과물을 처리한다. 로더의 파일 처리 후 난독화한다든지 특정 텍스트를 추출한다든지의 역할

    **[종류]**
    - [1. BannerPlugin](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html#61-bannerplugin)
    - [2. DefinePlugin](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html#62-defineplugin)
    - [3. HtmlWebpackPlugin](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html#63-htmlwebpackplugin)
    - [4. CleanWebpackPlugin](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html#64-cleanwebpackplugin)
    - [5. MiniCssExtractPlugin](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html#65-minicssextractplugin)


### references

- [https://d2.naver.com/helloworld/0239818](https://d2.naver.com/helloworld/0239818)
- [https://joshua1988.github.io/webpack-guide/devtools/source-map.html#%EC%86%8C%EC%8A%A4-%EB%A7%B5](https://joshua1988.github.io/webpack-guide/devtools/source-map.html#%EC%86%8C%EC%8A%A4-%EB%A7%B5)
- [https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html)
- [https://webclub.tistory.com/635](https://webclub.tistory.com/635)

### 끝