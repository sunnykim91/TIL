##Critical Rendering Path
- 브라우저가 하나의 화면을 그려내는 과정
1. HTML 데이터 파싱하여 DOM 트리만들기
2. CSS 파싱하여 CSSOM 트리 만들기
3. Javascript 파싱하고 실행
4. Render트리 만들기
5. 레이아웃 생성 reflow
6. 페인팅 repaint

<img src="https://github.com/user-attachments/assets/c957df30-f9b3-4e79-9c89-dca027fbfccf" width="600px" />

###파싱이란?
- 구문분석 즉, 의미 있는 단위로 분해하고 해석하는 과정

자세한 과정
- 브라우저의 렌더링 엔진은 HTML을 파싱하고 DOM 트리를 생성한다.
  - 문서를 읽으면서 토큰 단위로 분해하고(토큰화), 계층 구조를 가지도록 토큰을 객체로 변환하여 노드를 생성하여 트리로 구성하고(구조화), 어떤 역할을 하는지 파악(의미 해석) 하는 과정을 거친다.
  - 파싱과정에서 <link>, <style> 태그를 만나면 CSS를 파싱하고, CSSOM트리를 생성한다.
  - ** DOM, CSSOM의 생성은 병렬적으로 실행되며, 파싱과정에서 <script>태그를 만나면 DOM 생성을 중단하고 자바스크립트를 파싱하고 실행한다.
- CSSOM 트리의 생성
  - DOM 생성과 같은 과정을 반복
- 자바스크립트 파싱 및 실행
  - <script>태그를 body하단에 둘 수 밖에 없었던 문제점
      1. DOM이 아직 만들어지지않은상태에서 자바스크립트가 DOM을 제어하려고하면 오류가 난다.
      2. HTML을 빨리 화면에 보여줘야하는데 script파싱하느라 화면의 렌더링이 늦어진다.
  - async, defer : 스크립트를 백그라운드에서 다운로드함
     - defer : HTML 파싱이 끝난 이후에 스크립트가 실행 / DOMContentLoaded이벤트 전에 실행이 보장 / 여러 스크립트에서는 선언된 순서대로 실행
     - async : 다운로드 완료되면 즉시 실행, 이때 HTML파싱중단 / 실행순서가 보장되지않음 / 독립적인 스크립트에 적합(광고,분석도구)
     - ** 최신에는 <script type="module" src="module.js"></script> 이런식으로 사용하여서 module을 사용하여 모듈화된 코드를 작성하는데, 기본적으로 defer처럼 동작한다.

      ```html
         <html>
            <head>
              <script src="heavy-script.js"></script> <!-- 일반 script -->
              <script async src="analytics.js"></script> <!-- async -->
              <script defer src="ui-framework.js"></script> <!-- defer -->
            </head>
            <body>
              <h1>Hello World</h1>
            </body>
          </html>
      ```
      - 일반 스크립트는 HTML 파싱 중단하고 다운로드 후 바로 실행 / async는 백그라운드에서 다운로드하고, 완료되면 즉시 실행 / defer는 백그라운드에서 다운로드하고, HTML파싱 끝나면 실행
- 렌더트리 생성
  - DOM과 CSSOM트리를 연결하는 과정으로, 탐색하면서 일치하는 요소들을 찾아 레이아웃과 스타일 정보를 결합해 렌더트리를 생성한다.
- Reflow
  - 렌더트리를 가지고 HTML 요소의 레이아웃을 픽셀단위로 계산하여 렌더트리에 반영
- Repaint
  - 렌더트리를 배치하고, 배치가 끝난것들을 화면에 그리게 된다. 시각화과정(색상, 크기 등등) 


   
