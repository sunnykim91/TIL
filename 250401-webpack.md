## Webpack이란?
- 모듈 번들러 -> 프로젝트에서 사용하는 여러 파일들을 하나로 묶어주는 도구
- 코드를 최적화하고, 불필요한 부분을 제거하여 웹사이트의 성능을 향상시킨다.


## 웹팩의 주요 개념
- 모듈 번들링 - 프로젝트의 여러 모듈을 하나의 파일로 통합하여 관리, HTTP 요청 횟수를 줄이고 성능향상가능
- 로더 - js파일 외의 리소스를 웹팩이 이해할 수 있게 변환하는 도구, Typescript를 javascript로 scss를 css로
- 플러그인 - 기본기능을 확장하는 도구, 코드 최적화, HTML파일 생성, 환경변수주입 등의 역할



## 웹펙의 구성요소

### Entry
- 어플리케이션의 시작점 정의하는 설정
- import또는 require구문을 통해 하나의 파일 또는 여러개의 파일로 결합
- 기본적으로 단일엔트리로 SPA에서 많이 사용
- 다중엔트리로도 구성이 가능, 엔트리를 객체로 지정해 각기 다른 번들을 생성가능(MPA 구현시, 각 페이지별로 서로다른 스크립트 로드할때)

### output
- 번들 파일의 위치를 지정한다. 디렉토리와 파일 이름 지정
- 파일 네이밍을 제어한다. 여러시작점이 있을때 각각의 결과물을 체계적으로 관리, 각페이지에 필요한 코드를 분리하여 관리 가능
- ```typescript
  // webpack.config.js
  const path = require('path');
  
  module.exports = {
    entry: './src/index.tsx',                // 엔트리 포인트 설정
    output: {
      filename: 'bundle.js',                 // 번들된 파일의 이름
      path: path.resolve(__dirname, 'dist'), // 번들된 파일이 저장될 절대 경로
      clean: true,                           // 이전 빌드 결과물을 제거하고 새로 생성
    },
    // ... 기타 설정
  };
  ```


### 로더
- CSS, 이미지, 폰트, Typescript 등 다양한 파일을 읽고 Javascript모듈로 변환하여 Webpack이 번들링 할 수 있도록 도와준다.
- 파일을 읽은 후 필요한 형태로 변환하는 과정에서 역할을 한다.
- 주요 로더의 종류
  - 트랜스파일 babel-loader, ts-loader
  - 스타일 처리 css-loader, style-loader,sass-loader
  - 파일 처리 : 이미지 폰트 정적파일을 포함시키기 위한 처리 수행
 

### 플러그인
- 빌드된 결과물을 개선하고 반복작업을 자동으로 처리 , 로더 이후 추가작업 수행
- 주요 플러그인
  - HtmlWebpackPlugin : 번들링된 Javascript 파일을 자동으로 HTML파일에 포함시켜주는 Plugin , <script>태그를 관리할 필요가없고, 빌드할때마다 최신 번들 파일이 HTML에 삽입
  - ForkTsCheckerWebpackPlugin : Typescript 프로젝트의 성능을 향상시키는 도구, 대규모 프로젝트에서 타입 검사로 인한 빌드 시간 지연문제를 효과적으로 해결 가능
  - MiniCssExtractPlugin : CSS코드를 별도의 파일로 분리하는 도구, 분리된 CSS파일을 브라우저 캐싱기능을 가능하게함
  - dotenv-webpack : 환경변수를 관리, .env파일을 통해 개발/운영에 맞는 설정값 제공


### 소스맵
- 변환된 코드와 원본 소스 코드간의 매핑 정보를 제공하는 파일, 디버깅시에 원본코드를 기반으로 문제를 파악하고 수정할수있게 도와줌
- 트랜스파일링에 필요하다.
- 소스맵을 적용했을때 안적용했을때 차이는 원본소스파일이 그대로 표시(어디서에러났는지도) 되면서, 중단점 설정이나 변수검사등이 가능하다. 압축된 코드만 보여준다.
- 소스맵 사용시 주의점 :
   - 소스맵 생성은 빌드 시간을 증가시킴, 정확도가 높을 수록 디버깅엔 좋으나, 빌드가 느려짐, 적절한 균형이 필요
   - 보안 측면 : 원본코드가 노출될 수 있음. hidden-source-map  옵션 사용
   - ```typescript
     // webpack.config.js
        module.exports = (env, argv) => {
          const isProduction = argv.mode === 'production';
        
          return {
            // ...
            devtool: isProduction ? 'hidden-source-map' : 'eval-cheap-module-source-map',
            // ...
          };
        };
     ```
   - 위와같이 개발환경과 프로덕션환경으로 나누어서 관리하는게 좋음


### Webpack-merge 
- 환경(dev, production)별로 설정을 분리하고 병합
- ```typescript
  // webpack.dev.js

  const { merge } = require('webpack-merge');
  const common = require('./webpack.common.js');
  
  module.exports = merge(common, {
    mode: 'development', // 개발 모드 설정
    devtool: 'eval-cheap-module-source-map', // 빠른 빌드와 디버깅을 위한 소스맵 설정
    devServer: {
      open: true, // 개발 서버 시작 시 브라우저 자동 열기
      host: 'localhost', // 호스트 설정
      port: 3000, // 포트 번호 설정 (필요 시 추가)
      hot: true, // 핫 모듈 교체 활성화
    },
  });
  ```
- ```typescript
  // webpack.prod.js

  const { merge } = require('webpack-merge');
  const common = require('./webpack.common.js');
  
  module.exports = merge(common, {
    mode: 'production', // 프로덕션 모드 설정
    devtool: false, // 소스맵 비활성화
  });
  ```
