# CSR(Client Side Rendering)과 SSR(Server Side Rendering)

## SPA와 MPA

- **SPA (Single Page Application)**

하나의 HTML 파일을 기반으로 자바스크립트를 이용해 동적으로 화면의 컨텐츠를 바꾸는 방식의 웹 어플리케이션이다.

- **MPA (Multiple Page Application)**

사용자가 페이지를 요청할 때마다, 웹 서버가 요청한 UI와 필요한 데이터를 HTML로 파싱해서 보여주는 방식의 웹 어플리케이션이다.

전통적인 방식을 이용한다면, SPA가 사용하는 렌더링 방식은 CSR이고, MPA가 사용하는 렌더링 방식은 SSR이다. 각 방식의 동작방식과 장단점을 알아보고, 전통적인 방식을 벗어나, SPA에서도 적절히 SSR을 구현했을 때의 장점과 그 이유를 알아보자.

<br>

## CSR

<img src="../../images/frontend/CSR.png">

[이미지 출처](https://medium.com/@adamzerner/client-side-rendering-vs-server-side-rendering-a32d2cf3bfcc)

CSR에선 브라우저가 서버에 HTML과 JS 파일을 요청한 후 로드되면 사용자의 상호작용에 따라 JS를 이용해서 동적으로 렌더링을 시킨다.

### :+1: 장점

- 첫 로딩만 기다리면, 동적으로 빠르게 렌더링이 되기 때문에 사용자 경험(UX)이 좋다.
  - 유려한 페이지 전환과 인터랙션
- 서버에게 요청하는 횟수가 훨씬 적기 때문에 서버의 부담이 덜하다.

### :-1: 단점

- 모든 번들 파일이 로드되고 실행될 때까지 유저가 제대로 된 화면을 볼 수 없다.
  - FCP(First Contentful Paint)가 상당히 지연되며, TTI(Time to Interact)는 그보다 더 뒤에 일어난다.(퍼포먼스 저하)
  - 느린 인터넷 커넥션에서는 더 큰 문제가 된다.
  - 리소스를 청크(Chunk) 단위로 묶어서 요청할 때만 다운받게 하는 방식으로 완화시킬 수 있지만 완벽히 해결할 수는 없다.
- 검색엔진의 검색 봇이 크롤링을 하는데 어려움을 겪기 때문에 검색엔진 최적화(Search Engine Optimization)의 문제가 있다.
  - 구글 봇의 경우는 JS를 지원하지만, 다른 검색엔진의 경우 그렇지 않기 때문에 문제가 된다.

<br>

## SSR

<img src="../../images/frontend/SSR.png">

[이미지 출처](https://medium.com/@adamzerner/client-side-rendering-vs-server-side-rendering-a32d2cf3bfcc)

SSR에선 브라우저가 페이지를 요청할 때마다 해당 페이지에 관련된 HTML, CSS, JS 파일 및 데이터를 받아와서 렌더링을 시킨다.

### :+1: 장점

- 초기 로딩 속도가 빠르기 때문에 사용자가 컨텐츠를 빨리 볼 수 있다.
  - FCP가 CSR에 비해 빠르다
- JS를 이용한 렌더링이 아니기 때문에 검색엔진 최적화가 가능하다.

### :-1: 단점

- 매번 페이지를 요청할 때마다 새로고침 되기 때문에 사용자 경험이 SPA에 비해서 좋지 않다.
- 서버에 매번 요청을 하기 때문에 서버의 부하가 커진다.

<br>

## Performace Implications of Application Architecture

### SSR with Hydration

Hydration 아키텍처에서는 첫 웹 페이지 렌더는 SSR로 이루어지지만 그 이후부터 전환될 모든 페이지는 CSR로 이루어진다. 특히 이후에 이루어지는 이벤트 리스너 등록작업을 [Hydrate](https://reactjs.org/docs/react-dom.html#hydrate) 라고 부른다. React 에서는 Next.js 나 Nuxt.js 와 같은 메타 프레임워크로 구현이 가능하다. FCP 는 빨라지나 TTI 는 변함이 없다.

### Pre-rendering

이에 대한 대안으로 Pre-rendering 이 있다. Pre-rendering 에서는 빌드 타임에 모든 HTML을 렌더링한다. 이미 렌더링되어 있으므로 SSR하는데 걸리는 시간이 필요하지 않아 FCP는 더욱 빨라질 것이다. 쉽게 말해 정적으로 페이지를 렌더링하는 것이고, 유명한 툴로는 React 생태계의 Gatsby 가 있다. 물론 이도 단점이 있는데, 빌드 타임에 모든 페이지 생성을 끝내야 하기 때문에 항상 정적인 컨텐츠에만 활용 가능하다는 것이다.

### Streaming

또 다른 대안으로 SSR Streaming 이 소개되었다. Streaming 은 브라우저가 페이지 렌더링을 서버 응답이 끝나기 전에 시작할 수 있도록 렌더링에 필요한 데이터를 청크로 쪼개 내려주는 것이다. 이 방법을 적용하면 TTFB(Time To First Byte)를 향상시킬 수 있다. React 에서는 이미 [관련 API](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)가 있기 때문에 바로 적용 가능한 방식이다. 실제로 Spectrum 이라는 웹 서비스에서 [Streaming 기법을 적용한 사례](https://bit.ly/spectrum-ssr)가 소개되었다.

## Progressive Hydration

Progressive Hydration은 마찬가지로 Hydration을 수행하지만 한 번에 하지 않고 필요한 부분만 점진적으로 하는 것이다. 페이지가 로드되면 나머지 어플리케이션을 모두 로드하는 Full Hydration과는 달리 Progressive Hydration에서는 상황 혹은 유저의 인터랙션에 따라 필요한 부분을 로드한다. React에서는 아직 지원하지 않고있는 기능이지만 별도의 라이브러리를 사용하면 비슷하게 구현이 가능하고, 차후에 React Suspense를 통해 지원할 계획이다. Airbnb는 이미 Progressive Hydration을 적용해 TTI를 극적으로 향상시켰다고 한다. React Progressive 예제는 이 [레포지토리](https://bit.ly/react-progressive)에서 확인할 수 있다.

## 참고

- [Client-Side Rendering versus Server-Side Rendering!](https://altalogy.com/blog/client-side-rendering-vs-server-side-rendering/)
- [What are Single Page Applications(SPA)?](https://dev.to/kendyl93/what-are-single-page-applications-spa-32bh)
- [SPA에서의 SSR과 CSR의 차이 + 벨로퍼트님 댓글](https://velog.io/@rjs1197/SSR과-CSR의-차이를-알아보자)
- [The Benefits of Server Side Rendering Over Client Side Rendering](https://medium.com/walmartlabs/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8)
- [(Google I/O 2019. Rendering on the Web: Performance Implications of Application Architecture](https://hyunseob.github.io/2019/05/26/google-io-2019-day-3/)
- [SEO를 위한 Technical Skill](https://m.blog.naver.com/placebo1353/221916852149)
- [SSR과 NEXT.js](https://velog.io/@jeff0720/Next.js-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4-%EB%B6%80%ED%84%B0-%EC%8B%A4%EC%8A%B5%EA%B9%8C%EC%A7%80-%ED%95%B4%EB%B3%B4%EB%8A%94-SSR-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95)
