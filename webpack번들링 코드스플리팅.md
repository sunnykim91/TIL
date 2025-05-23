# webpack번들링 코드스플리팅 옵션
- 목적 : 번들 크기를 최적화하고 브라우저 캐싱을 효율적으로 활용하기 위함
- 공통 모듈을 별도 파일로 분리해서 중복 코드 제거
- 큰 번들을 여러작은파일로 분할해서 병렬 다운로드 및 초기로딩시간 개선


```typescript
splitChunks: {
 // 'async': 동적 import된 비동기 모듈만 분할 대상으로 함
 // 'initial': 초기 로드에 필요한 모듈만 분할 대상으로 함
 // 'all': 모든 모듈(동기, 비동기)을 분할 대상으로 함
 chunks: 'async', 
 
 // 분할된 청크의 최소 크기 (바이트 단위, 약 20KB)
 // 이 크기보다 작은 청크는 분할되지 않고 기존 청크에 포함됨
 minSize: 20000,
 
 // 청크 분할 후 남은 청크의 최소 크기 (바이트 단위)
 // 0이면 최소 크기 제한 없음을 의미
 minRemainingSize: 0,
 
 // 모듈이 최소 몇 개의 청크에서 사용되어야 분할 대상이 되는지 지정
 // 1은 모든 모듈이 분할 대상이 될 수 있음을 의미
 minChunks: 1,
 
 // 한 번의 HTTP 요청에서 병렬로 로드할 수 있는 비동기 청크의 최대 수
 // 이 값을 초과하면 청크 분할이 중지되어 요청 수를 제한함
 maxAsyncRequests: 30,
 
 // 초기 페이지 로드 시 병렬로 요청할 수 있는 청크의 최대 수
 // 이 값을 초과하면 청크 분할이 중지되어 초기 로드 요청 수를 제한함
 maxInitialRequests: 30,
 
 // 청크 크기가 이 값(바이트)을 초과하면 minSize, maxAsyncRequests, maxInitialRequests 설정과 
 // 관계없이 강제로 분할됨 (약 50KB)
 enforceSizeThreshold: 50000,
 
 // 특정 모듈 그룹에 대한 분할 규칙을 정의하는 캐시 그룹
 cacheGroups: {
   // node_modules에서 가져온 외부 라이브러리를 위한 그룹
   defaultVendors: {
     // node_modules 폴더 내의 모듈을 대상으로 함
     // 정규식 또는 함수로 지정 가능
     test: /[\\/]node_modules[\\/]/,
     
     // 캐시 그룹 간 우선순위 (-10은 기본값보다 높음)
     // 모듈이 여러 캐시 그룹 조건에 맞을 경우 우선순위가 높은 그룹에 할당됨
     priority: -10,
     
     // true인 경우 이미 분할된 청크를 재사용
     // 동일한 모듈이 여러 번 분할되는 것을 방지하여 중복 코드 제거
     reuseExistingChunk: true,
   },
   
   // 기본 분할 규칙 (node_modules 외의 모듈에 적용)
   default: {
     // 최소 2개 이상의 청크에서 사용되는 모듈만 분할
     // 여러 페이지에서 공통으로 사용되는 모듈을 별도 파일로 분리하여 캐싱 효율 증가
     minChunks: 2,
     
     // 우선순위가 낮음 (-20)
     // defaultVendors보다 우선순위가 낮아 vendor 모듈이 먼저 처리됨
     priority: -20,
     
     // 이미 분할된 청크 재사용
     reuseExistingChunk: true,
   },
 },
},
```
