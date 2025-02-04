# Hydration이란?
- 브라우저에서 HTML을 렌더링 할 때 페이지의 컴파일된 HTML에 Javascript 동작을 다시 추가하는 프로세스
- 즉, 서버에서 미리 렌더링된 HTML을 클라이언트에서 다시 활성화하는 과정(정적인 HTML코드에 동적으로 js를 붙이는)
- 목적 : 서버에서 미리 렌더링된 HTML과 클라이언트 측의 React 컴포넌트를 동기화 하는 것
- 주요 단계 :
  - 1. 초기 HTML 전송 : 서버는 정적 HTML 파일을 전송하여 초기 페이지 로드를 보장
    2. Javscript 번들 로드 : 브라우저는 필요한 Javascript 파일들을 다운로드 실행
    3. 가상 DOM 생성 : React는 서버에서 생성된 HTML과 동일한 구조의 가상 DOM을 만듬
    4. 동기화 : React는 가상돔과 실제 돔을 비교하여 차이점을 찾아내고, 이벤트 리스너를 다시연결 
- CSR에서는 하이드레이션이 발생하지않음. 애초에 미리 렌더링된 HTML이 없기 때문에
- 레이아웃 시프트 : 하이드레이션이 진행되면서 요소가 갑자기 이동하는 문제 -> 클라이언트에서 동적 데이터가 로딩될때 발생 -> 스켈레톤이나 레이아웃 고정해결
- 하이드레이션 중에는 로딩스피너로 사용자에게 빈화면을 보여주지 않기 위해서 하기도함

# NEXT.js
- 등장 배경 :
  - CSR의 한계(초기 로딩속도 느림, SEO 어려움 등)
  - React로 SSR을 구현하려면 번들링, 라우팅, 코드 분할을 일일히 설정해야함
- NEXT의 주요기능
  - Zero Configuration: 복잡한 설정 없이 바로 SSR 사용 가능
  - 파일 기반 라우팅
  - 데이터 패칭, 스타일링 등등
  - SSR에서 여러가지 기능들을 쉽게 사용할수있도록 추상화한것
 
# SSG 방식?
- 빌드 타임에 HTML을 생성하는 랜더링 방식
- 모든 페이지가 빌드 시점에 미리 만들어진다. 한번 생성된 페이지는 모든 사용자가 동일한 내용을 보게됨
- 블로그를 만든다 - SSG 사용
- 장점 :
  - 사용자가 페이지 접속시 즉시 완성된 HTML을 받아볼 수 있다.
  - 검색엔진이 크롤링 할때도 모든 컨텐츠가 있다->SEO에 최적화
  - 한번 빌드된 페이지는 CDN에 캐싱되어 어디서 접속하든 매우 빠름
- 실제 블로그를 만들때 추천하는 방식
  - 1. 메인 컨텐츠 (글, 이미지) → SSG
     - 자주 변경되지 않는 컨텐츠
     - 빠른 로딩 속도 필요
    2. 댓글 기능 → CSR
       - 동적으로 변하는 부분
       - 메인 컨텐츠 로드 후 나중에 로드해도 OK
    3. 검색 기능 → SSR 또는 CSR
       - 실시간 검색 결과 필요

## Next.js의  ISR(Incremental Static Regeneration)
```typescript
// pages/posts/[id].js

export async function getStaticProps({ params }) {
  const post = await getPost(params.id);
  
  return {
    props: {
      post,
    },
    // 여기가 핵심!
    revalidate: 60 // 60초마다 재검증
  }
}

export async function getStaticPaths() {
  // 처음 빌드 시에는 자주 접속하는 글만 미리 생성
  const popularPosts = await getPopularPosts();
  
  return {
    paths: popularPosts.map((post) => ({
      params: { id: post.id }
    })),
    fallback: 'blocking' // 중요! 나머지 페이지는 요청 시 생성
  }
}
```
- 전체 사이트를 다시 빌드할 필요가 없음
- 변경된 페이지만 재생성 가능
- 성능과 리소스를 효율적으로 관리 가능
- 처음 빌드 시에는 중요한 페이지만 생성
  나머지는 첫 요청 시에 생성되고 캐시됨
  revalidate 시간이 지난 후 요청이 들어오면 백그라운드에서 페이지 재생성
  사용자는 항상 캐시된 페이지를 먼저 보게 되어 빠른 응답 가능
- <img src="https://github.com/user-attachments/assets/9d53e204-82b3-4b41-916a-ff17d782135d" width="600px" />
  사용자1이 해당 페이지에 진입하면 이때 부터 60초 동안은 어느 사용자가 들어오더라도 미리 생성해두었던 페이지를 제공해 준다. 이후 60초가 지나면 NextJS 백그라운드에서 해당 페이지의 업데이트가 이루어지고 이 업데이트가 완료되면 앞으로 새롭게 만들어진 페이지를 사용자에게 제공해주는 방식이다. 이를 통해 SSG 의 성능상 이점을 챙기면서도 사용자에게는 업데이트된 내용을 제공해줄 수 있다. 
- 이커머스에서 가격,재고는 변동되지만 상품 설명과 이미지는 변경이 안됨. 일정주기로 가격/재고는 업데이트
- 뉴스기사에서 기사내용은 바뀌지않으나, 댓글,조회수는 실시간 변동, 일정주기로 새로운 댓글 반영


## 빌드와 SSG, SSR, CSR
- package.json에 스크립트 부분에서 build명령어를 보면, 어떻게되어있는지에따라 갈린다.
- ```typescript
  // React (Create React App) - CSR
  {
    "scripts": {
      "build": "react-scripts build"
    }
  }
  // 결과: build/
  //   ├── index.html      # 빈 HTML
  //   └── static/
  //       ├── js/         # 번들링된 JS
  //       └── css/        # 스타일
  
  // Next.js - SSG/SSR 가능
  {
    "scripts": {
      "build": "next build"
    }
  }
  // 결과: .next/
  //   ├── static/         # 정적 파일
  //   ├── server/         # SSR용 서버 파일
  //   └── static/html/    # SSG로 생성된 HTML
  
  // Gatsby - SSG 전용
  {
    "scripts": {
      "build": "gatsby build"
    }
  }
  // 결과: public/
  //   ├── index.html      # 완성된 HTML
  //   ├── pages/          # 각 페이지 HTML
  //   └── static/         # 정적 파일
  ```
- 위와같이 어떤 프레임워크냐 도구냐에따라 다르게빌드가 된다. 다르게 빌드가 된것을 가지고 배포하게되면


