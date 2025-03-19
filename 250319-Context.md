## React의 Context API
- React의 내장기능으로 컴포넌트 트리를 통해 데이터를 명시적인 Props 전달 없이도 공유할수있게하는 것
- 장점 : 별도 설치 필요없다. Prop Drilling을 방지한다.
- 단점 : 렌더링 최적화가 어렵다. Context값이 변경되면 Context를 구독하는 모든 컴포넌트가 리렌더링된다. (useMemo, useCallbakc, React.memo활용하자)
        여러개의 Context를 사용하면 Provider hell현상이 있을수도 있다.
- 전역적으로 상태를 관리하기엔 좋으나, 제일 중요한것은 컴포넌트를 재사용하기가 어려워진다. -> 컴포넌트 합성을 사용하는게 낫다.
- 

## 컴포넌트 합성
- 데이터가 명시적으로 어디서 받아서 어떻게 보여주겟다를 볼수있다.
- 재사용성 향상 -> 테스트가 용이
- 예제
```typescript
import React from 'react';

// 1. 기본적인 컴포넌트 합성 (children prop 사용)
function Card({ children, title }) {
  return (
    <div className="card" style={{ 
      border: '1px solid #ddd', 
      borderRadius: '8px',
      padding: '16px',
      margin: '16px 0'
    }}>
      <h2>{title}</h2>
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

// 2. 사용 예시
function App() {
  return (
    <div className="app">
      <h1>컴포넌트 합성 예제</h1>
      
      {/* Card 컴포넌트 안에 다른 컴포넌트들을 합성 */}
      <Card title="공지사항">
        <p>이것은 카드 내부에 포함된 컨텐츠입니다.</p>
        <button>자세히 보기</button>
      </Card>
      
      <Card title="프로필">
        <img 
          src="https://via.placeholder.com/50" 
          alt="프로필 이미지"
          style={{ borderRadius: '50%' }}
        />
        <h3>홍길동</h3>
        <p>프론트엔드 개발자</p>
      </Card>
    </div>
  );
}

// 3. 특수 슬롯을 사용한 고급 합성 패턴
function Layout({ header, sidebar, main, footer }) {
  return (
    <div className="layout" style={{ display: 'grid', gridTemplateRows: 'auto 1fr auto', minHeight: '100vh' }}>
      <header style={{ padding: '16px', backgroundColor: '#f0f0f0' }}>
        {header}
      </header>
      
      <div style={{ display: 'grid', gridTemplateColumns: '200px 1fr' }}>
        <aside style={{ padding: '16px', backgroundColor: '#f8f8f8' }}>
          {sidebar}
        </aside>
        
        <main style={{ padding: '16px' }}>
          {main}
        </main>
      </div>
      
      <footer style={{ padding: '16px', backgroundColor: '#f0f0f0' }}>
        {footer}
      </footer>
    </div>
  );
}

// 레이아웃 사용 예시
function PageWithLayout() {
  return (
    <Layout
      header={<h1>웹사이트 제목</h1>}
      sidebar={
        <nav>
          <ul>
            <li><a href="#">홈</a></li>
            <li><a href="#">소개</a></li>
            <li><a href="#">서비스</a></li>
            <li><a href="#">연락처</a></li>
          </ul>
        </nav>
      }
      main={
        <div>
          <h2>메인 콘텐츠</h2>
          <p>여기에 페이지의 주요 내용이 들어갑니다.</p>
          <Card title="중첩된 카드">
            <p>레이아웃 안에 다른 합성 컴포넌트를 중첩할 수 있습니다.</p>
          </Card>
        </div>
      }
      footer={
        <p>&copy; 2025 컴포넌트 합성 예제</p>
      }
    />
  );
}

export default App;
```
