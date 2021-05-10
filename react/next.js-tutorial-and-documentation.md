# Next.js

1주차는 next.js로 정했다. 요즘 정말 핫하기도 하고, 여기저기에서 많이 쓰이니 나중에 제대로 쓰기 전에 next.js와 SSR에 대해서 정리할 필요성을 느껴서.

## Next.js 개념

> Next.js is an open-source React front-end development web framework created by Vercel that enables functionality such as server-side rendering and generating static websites for React based web applications

위키피디아에 있는 설명인데 깔끔하고 명시적인것같아 가져와봤다.

- framework
- server-side rendering

### server-side rendering이란?

사실 백엔드 개발자라면 서버사이드렌더링은 낯설지 않을 것이다. 다만 전형적인 CSR (Client-Side Rendering)인 리액트에서 SSR을 사용한다는것이 잘 와닿지 않아 좀 탐색해보았다.

용어가 조금 헷갈리긴 하지만 React, 즉 frontend에서 SSR을 사용할 경우 백엔드 서버가 아닌 프론트용 서버를 두고 렌더링을 서버사이드에서 하여 내려준다.


## next.js 튜토리얼

### create-next-app

next.js에서 제공하는 간단한 blog 튜토리얼을 진행해보았다

```
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter"
```

> 실제로 cra처럼 사용할때에는 `npx create-next-app`을 사용하면 될듯.

기본적인 경로는 다음과 같다.

```
$ tree -L 1
.
├── README.md
├── node_modules
├── package-lock.json
├── package.json
├── pages
└── public
```

일반적인 CRA와 비슷하지만 `pages`라는 디렉토리가 있는게 특이하다.


실제로 서버를 띄우고 이것저것 만져보자.

```
npm run dev
```

### navigate between pages

next.js에서는 `page`라는 개념이 사용된다. 이는 `pages` 디렉토리 하위에서 exported되는 컴포넌트로, 파일명에 기반해서 라우팅이 된다.

위의 blog 예시의 pages 디렉토리는 다음과 같다.

```
$ tree pages
pages
└── index.js
```

- `pages/index.js`: index는 `/`로 routing된다.
- `pages/posts/first-post.js`는 `/posts/first-post`로 라우팅된다.

기존 프로젝트에는 index.js만 있으니 `pages/posts/first-post.js`를 생성하고 라우팅 해보자.

간단하게 `first-post.js`를 아래와 같이 생성하고 `/posts/first-post` url을 입력하면 해당 페이지로 라우팅되는것을 볼 수 있다.

```
export default function FirstPost() {
  return <h1>First Post</h1>
}
```

#### Link component

next.js에서 페이지간의 이동은 <a>태그를 래핑하고 있는 `Link` 컴포넌트를 사용한다.

사용법은 심플.

`pages/index.js`의 title 부분을 변경해보자.

```
import Link from 'next/link'
...
<h1 className="title">
  Read{' '}
  <Link href="/posts/first-post">
    <a>this page!</a>
  </Link>
</h1>
...
```

그리고 위에서 만들었떤 `first-post.js`에도 홈으로 돌아가는 link를 추가해보자.

```
import Link from 'next/link'

export default function FirstPost() {
  return (
    <>
      <h1>First post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </>
  )
}
```

`Link` 컴포넌트는 client-side navigation을 가능하게 해준다고 한다.

> client-side-navigation이란? page transition이 브라우저가 아닌 javascript에서 일어나는 것. full refresh가 되지 않아서 더 빠르다고함.

> 실제로 개발자도구에서 background color를 바꾸고 routing 해보면 다른 페이지로 넘어가도 변경된 css가 바뀌지 않는다.

![image](https://nextjs.org/static/images/learn/navigate-between-pages/client-side.gif)

pages는 다양한 장점이 있다고 소개된다.
- code splitting: net.js가 자동으로 code splitting을 적용하여 해당 페이지에 필요한 코드만 가져옴
  - 수백 페이지가 있어도 홈 로딩에 크게 오랜 시간이 걸리지 않을 수 있음
- prefetching: `Link` 컴포넌트가 브라우저 뷰포트에 나타나면 next.js가 백그라운드에서 해당 페이지에 필요한 코드를 prefetch해줌
  - 해당 페이지를 클릭했을때 이미 로드되어있기 때문에 빠르게 페이지 로드 가능

확실히 ssr이 필요하거나 많은 페이지를 가진 애플리케이션에 적합해보인다.

### Assets, Metadata, and CSS

next.js에서는 CSS, Sass를 built-in으로 지원함.

- `public/` 디렉토리 안에 asset을 넣고 사용할 수 있음.
- 특이한건 [next/image](https://nextjs.org/docs/api-reference/next/image) 컴포넌트를 제공해서 이를 사용할 수 있다는점이다. 실제로 얼마나 쓰일지는 잘 모르겠다.
_ next.js는 build time에 이미지 최적화를 하지 않고 유저의 요청이 있을때 on-demand로 최적화를 하기 때문에 빌드 시간이 줄어든다고 함. SSR의 또다른 장점이라고 할 수 있겠따.
- 이미지는 디폴드로는 lazy load된다. (뷰포트에 들어오면 로드)

그 이외에 [next/head](https://nextjs.org/docs/api-reference/next/head)도 제공함.

#### css

- css는 vercel의 [style-jsx](https://github.com/vercel/styled-jsx)가 내장되어있다.
- global css는 `pages/_app.js`에서만 불러와서 사용 가능함.
- 보통 convention으로 root directory에 `styles` 디렉토리를 만들고 `global.css`를 넣어서 사용

### Pre-rendering and data fetching

[Pre-rendering](https://nextjs.org/docs/basic-features/pages#pre-rendering)
- next.js는 모든 페이지를 pre-rendering 함
  - client-side의 javascript에서 렌더링하지 않고 각 페이지의 html을 미리 만들어놓고 서빙함
  - 더 나은 퍼포먼스와 SEO 가능
- 렌더링된 각 페이지의 HTML은 페이지에 필수적인 최소한의 javascript 코드를 가지고 있음.
- 브라우저에 페이지가 로드되면 코드가 동작하여 페이지를 interactive하게 만들어줌

실제로 브라우저에서 javascript를 disable 하고 앱을 실행해보면 pre-rendering이 실행되기 떄문에 javascript 없이도 앱이 로드되는것을 볼 수 있다.

![image](https://nextjs.org/static/images/learn/data-fetching/pre-rendering.png)

![image](https://nextjs.org/static/images/learn/data-fetching/no-pre-rendering.png)

#### two forms of pre-rendering

next.js는 두가지 방법의 pre-rendering을 제공해줌.

- [Static Generation](https://nextjs.org/docs/basic-features/pages#static-generation-recommended)
  - build time에 HTML을 생성하는 방식. 각 request에서 재사용됨
- [Server Side Rendering](https://nextjs.org/docs/basic-features/pages#server-side-rendering)
  - 각 request에 HTML을 생성하는 방식

![image](https://nextjs.org/static/images/learn/data-fetching/static-generation.png)

![image](https://nextjs.org/static/images/learn/data-fetching/server-side-rendering.png)


기본적으로 next.js는 각 페이지에 어떤 방식의 pre-rendering을 사용할지 선택 가능하게 되어있다.

각각을 언제 사용해야할까?

문서에 따르면 가능하면 Static generation을 사용하라고 권장
- 한번 빌드되고 나서는 CDN으로부터 서빙되기 떄문에 SSR보다 훨씬 빠름
- 유저의 요청 이전에 pre-render 할 수 있다면 Static generation을 사용

페이지가 자주 업데이트 되거나 유저의 요청에 따라 컨텐츠가 변한다면 SSR

#### Static Generation

Static generation은 데이터 없이도 가능하지만, 외부 데이터를 fetch하고도 가능하다.

`getStaticProps`를 async로 사용.
- 빌드타임에 getStaticProps가 실행되고
- 그때 필요한 데이터를 fetch해서 페이지로 props를 보내줌

> development mode에서는 getStaticProps는 매 request마다 실행된다고 한다.

실제로 getStaticProps을 사용해보자. blog data로 생성한 예시 코드.
- pages/index.js에 추가
- getSortedPostsData는 data를 fetch해서 반환하는 함수

```
import { getSortedPostsData } from '../lib/posts'

export async function getStaticProps() {
  const allPostsData = getSortedPostsData()
  return {
    props: {
      allPostsData
    }
  }
}

export default function Home({ allPostsData }) {
...
```

home 컴포넌트에서 allPostsData를 props로 사용이 가능하다.

- 위 예시에서는 디렉토리의 마크다운 파일을 읽었지만, api나 database에서 직접 쿼리하는것도 가능하다.
  - 그 이유는 getStaticProps가 무조건 server-side에서만 실행되기 때문
- getStaticProps는 반드시 `page`에서만 사용되어야 한다.

#### Server side rendering: Fetching data at request time

만약 build time이 아니라 request time에 데이터를 가져오고 싶다면 SSR을 사용해야한다.

![image](https://nextjs.org/static/images/learn/data-fetching/server-side-rendering-with-data.png)

이를 위해서 `getStaticProps` 대신 `getServerSideProps`를 사용하면 됨.

`getServerSideProps`는 매 request time에 실행되기 때문에 `context`는 request에 특화된 정보를 담고있다.

```
export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    }
  }
}
```

> TTFB (Time To First Byte)가 CDN에서 캐시되는 static props보다 느리기 때문에 데이터가 반드시 request time에 fetch 되어야 하는 경우에만 사용하는게 좋다.

#### client-side rendering

data를 pre-render할 필요가 없다면 [Client-side rendering](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side) 전략을 사용하는것도 좋음

![image](https://nextjs.org/static/images/learn/data-fetching/client-side-rendering.png)

위 그림에서 설명된 것처럼
- 외부 데이터 없이 페이지를 render 한 뒤,
- 페이지가 로드될 때 javascript에서 데이터를 fetch해온 뒤 나머지를 그려줌
- 일반적인 react 애플리케이션에서 실행되는 방식

다만 이 경우에는 next.js에서 제공하는 [SWR](https://swr.vercel.app/) hook을 사용할것을 권장하고 있다.
- caching, revalidation, focus tracking, refetching on interval 등을 제공해준다고 함.

```
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetch)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```

#### 정리
페이지를 pre-rendering 하는 방식
- static generation: build time에 모든 html을 렌더링 하고 CDN에서 서빙
- server-side rendering: request time에 html을 렌더링하는 방식

데이터를 fetch하는 방식
- Fetch data from server
  - build time
    - getStaticProps: build time에 (static generation) 데이터를 fetch해서 props로 넣어주는 방식
  - request time
    - getServerSideProps: request time에 (server-side rendering) 데이터를 fetch
- Fetch data from client
  - useSWR: pre-render시에 데이터를 fetch하지 않고 client-side에서 데이터를 fetch

> 작성한 코드는 [nextjs-tutorial repository](https://github.com/bartkim0426/nextjs-tutorial)에 올려두었다.


### Dynamic routes

외부 데이터에 따라 page path가 변하는 경우 [dynamic route](https://nextjs.org/docs/routing/dynamic-routes) 사용 가능

![image](https://nextjs.org/static/images/learn/dynamic-routes/page-path-external-data.png)

이전에 만든 블로그 포스트의 url을 dynamic route로 구현 예시

- 각각의 post들을 `/posts/<id>`로 생성
- `ssg-ssr.md`는 `/posts/ssg-ssr`, `pre-rendering.md`는 `posts/pre-rendering`으로 생성

전체적인 흐름은 다음과 같음

- `pages/posts` 하위에 `[id].js` 생성
  - `[]`로 감싸진 페이지는 next.js에서 dynamic route를 의미함
- 해당 파일 안에 post page를 렌더할 컴포넌트 추가
- 해당 파일에 `getStaticPaths`를 사용하여 `id`로 사용할 값 리스트 반환
- `getStaticProps` 사용하여 필요한 `id`값을 가져옴

정리하면 다음과 같음

`/posts/<id>`로 dynamic route를 구현하고 싶으면
- `<id>`가 dynamic한 값이 됨
- `/pages/posts/[id].js` 생성: 이 파일은 다음으로 구성
  - 해당 페이지를 렌더할 react component
  - `getStaticPaths`: possible values for id
  - `getStaticProps`; fetch necessary data for the post with id

tutorial의 예시가 아주 깔끔한 코드라서 한 번 따라해보면 좋을듯.


### Render Markdown, Polishing

이부분은 `remark` 라이브러리를 사용하여 markdown을 렌더링하는 부분과 css로 꾸미는 부분이다.

딱히 next.js와 관련이 없는거같아서 정리하지 않았다.

### Dynamic Route detail


- `getStaticPaths`도 `getStaticProps`와 마찬가지로 development 환경일 때에는 매 요청마다 실행되고, production 환경일 경우에는 빌드타임에 실행된다.
#### `Fallback` option: `getStaticPaths`에 줄 수 있는 옵션.
- false로 실행되면 route가 발견되지 않으면 404
- true라면 getStaticProps의 실행방식이 변경됨
  - `getStaticPaths`가 최초 build time에 실행됨
  - 만약 buildtime에 생성된 path가 아니라면 404대신 첫 요청으로 생성도니 "fallback" 버전의 페이지가 서빙됨
  - `fallback: 'blocking'` option도 있음

#### Catch-all routes
- `...`을 추가해서 extend path 가능
- `pages/posts/[...id].js`: `/posts/a`, `/posts/a/b`, `/posts/a/b/c`... 모두 match
- 이를 받기 위해서 `getStaticPaths`에서 `id` 키로 array를 반환해야됨

```
return [
  {
    params: {
      // Statically Generates /posts/a/b/c
      id: ['a', 'b', 'c']
    }
  }
  //...
]
```

물론 `getStaticProps`도 array를 받게된다.

#### Router

next.js router에 직접 접근하고 싶다면 `useRouter` hook을 사용할 수 있음. ([next/router](https://nextjs.org/docs/api-reference/next/router))

#### Custom 404

`pages/404.js`를 만들면 빌드타임에 생성된 이후에 서빙됨.


### API Routes

next.js 앱 안에서 API endpoint를 만들 수 있게 해주는 기능 (??)

`pages/api` 안에 함수를 생성해서 만들 수 있다.

> 어디에 쓰일지 감이 안오긴한다.

```
// req = HTTP incoming message, res = HTTP server response
export default function handler(req, res) {
  // ...
}
```

hello를 return하는 간단한 `hello.js` 예시

```
export default function handler(req, res) {
  res.status(200).json({ text: 'Hello' })
}
```

> request는 nodejs의 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage), response는 [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse) 객체


### API Routes detail

#### `getStaticProps`이나 `getStaticPaths`에서 API Route를 fetch하지 말것
- 대신 getStaticPaths, getStaticProps에서 직접 server-side code를 적으라고 함
- getStaticProps, getStaticPaths는 client-side가 아닌 server-side에서 실행되기 때문
- ?? 그래도 왜 안되는지 잘 모르겠음.

#### good usecase: handling form input

form input을 힌들링 할 때 API Route로 POST 요청을 보내서 client bundle에 포함시키지 않고 server-side code 작성 가능

```
export default function handler(req, res) {
  const email = req.body.email
  // Then save email to your database, etc...
}
```

일반적으로 backend api를 콜하면 될것같은데 왜 좋은 예시인지 잘..


#### Preview mode

Static Generation은 headless CMS에 적합하지만 수정사항에서 preview를 보기에는 적합하지 않음 (빌드타임에 generate 되므로)

이를 위해서 API route를 활용한 preview mode를 제공한다고 함. markdown preview 같은거 만들 때 써보면 재미있을듯.

### Deploying next.js app

vercel에 배포하는 예시. 굳이 vercel을 써서 좋은점은 잘 모르겠지만 personal로 쓰면 무료라서 한번 배포해봄.

확실히 무조건 서버가 필요하다는 점이 next.js의 가장 큰 단점인거같다.

- github에 push
- vercel 계정 만들기
- github repository 지정한 이후에 배포

정말 간편하게 배포가 되긴 한다.

main branch에 머지되었을 때 빌드, 배포도 자동으로 됨.. 사용성은 아주 좋은듯하다.


아래는 vercel로 배포된 next.js blog 예시이다.

https://nextjs-tutorial-iota-rouge.vercel.app/



# next.js documentation

[next.js 문서](https://nextjs.org/docs/getting-started)

문서는 전부 정리하지는 않고 잘 모르거나 하는 개념만 정리해둘 예정

-> 딱히 전부 읽을 필요 없이 필요할 때 필요한 부분을 읽는게 좋을듯.

## Basic features

### pages
- static generation (recommended)
  - production에서 `next build` 명령어를 실행시킬 때 HTML이 생성됨 (build time)
- server-side rendering
  - 각 request마다 HTML 생성됨.

### [Environment variables](https://nextjs.org/docs/basic-features/environment-variables)

- `.env.local`로 환경변수 로드 가능
- 환경변수를 브라우저에 노출 가능

#### Loading Env

next.js에는 `.env.local`의 환경변수를 `process.env`로 로드해주는 빌트인 기능이 있음.

아래는 `.env.local`의 예시.

```
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

이는 node.js 환경에서 자동으로 `process.env.DB_HOST`의 형태로 로드됨.

이를 data fetch나 API routes에서 사용이 가능함.

`getSteaticProps`에서 환경변수를 사용한 예

```
// pages/index.js
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  })
  // ...
}
```

> server-only secret을 보호하기 위해서 Next.js는 `process.env.*`를 빌드타임에 해당 변수로 대치시킴.

> 즉, `process.env`는 javascript object가 아니기 때문에 object destructuring을 할 수 없음. 변수로 할당해서 쓸 수 없고 `process.env.*` 형태로만 사용해야한다.


next.js는 `.env*` 파일의 `$VAR` 변수를 자동으로 expand 해줌. `$` 캐릭터를 사용하려면 escape 하면 됨 (`\$`)

```
# .env
HOSTNAME=localhost
PORT=8080
HOST=http://$HOSTNAME:$PORT
```

### Exposing environment variable to browser

default로 `.env.local`의 모든 환경변수는 node.js 환경에서만 사용 가능하기 때문에 브라우저에 노출되지 않음.

이를 노출시키기 위해서는 `NEXT_PUBLIC_` prefix를 사용하면 됨.

```
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

이는 node.js 환경에서 `process.env.NEXT_PUBLIC_ANALYTICS_ID`로 자동으로 로드되고, 코드에서 사용 가능.

뿐만 아니라 브라우저에서도 사용 가능하기 때문에 빌드타임 이후에도 사용 가능.

```
// pages/index.js
import setupAnalyticsService from '../lib/my-analytics-service'

// NEXT_PUBLIC_ANALYTICS_ID can be used here as it's prefixed by NEXT_PUBLIC_
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

function HomePage() {
  return <h1>Hello World</h1>
}

export default HomePage
```

### Default Environment Variables

대개는 `.env.local` 파일만 필요하지만, 종종 dev 환경이나 production 환경에서 env를 추가해야할 경우가 생김. (`next dev`, `next start`)

next.js는 default environment를 다음과 같이 사용하게 해줌
- `.env`: all environment
- `.env.development`: development environment
- `.env.production`: production environment

`.env.local`은 항상 default를 override함.

> `.env*.local`이 secret을 보관하고, `.gitignore`에 추가되어야한다. `.env`, `.env.development`, `.env.production`은 기본으로 레파지토리에 저장함.

아주 헷갈리게 되어있는듯.. 이부분 안봤으면 당연히 `env.production`에 secret들 넣었을듯


### Environment variables on vercel

vercel로 배포한다면 (아마 거의 그렇겠지만) project settings에서 env variable을 설정할 수 있음.

## Routing

### Dynamic routes

`[param]` 형태로 dynamic route를 지원

이 경우 `router.query`로 해당 파라미터들을 받아올 수 있음.

`/pages/post/[pid].js`의 예시

```
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```

url에 해당하는 query 객체가 생성됨.

`/posts/abc?foo=bar`의 경우


```
{
  "foo": "bar",
  "pid": "abc"
}
```

#### Catch all routes

`...`을 사용해서 모든 path를 캐치할 수 있음.

`pages/post/[...slug].js`는 아래 모두 매칭.

- /post/a
- /post/a/b
- /post/a/b/c

이는 query object에 array로 추가됨.

```
{"slug": ["a", "b", "c"]}
```

#### Optional catch all routes

위에서는 한 개 이상의 slug가 필수적이였는데, double bracket을 사용해서 이를 optional로 쓸 수도 있다.

`pages/post/[[...slug]].js`는 slug가 없는것도 매칭됨.
- /post
- /post/a
- /post/a/b
- /post/a/b/c

#### 주의사항

- predefined route는 dynamic route보다 우선권을 가짐. (dynamic route가 모든 route를 캐치한다고 해도)
  - pages/post/create.js - Will match /post/create
  - pages/post/[pid].js - Will match /post/1, /post/abc, etc. But not /post/create
  - pages/post/[...slug].js - Will match /post/1/2, /post/a/b/c, etc. But not /post/create, /post/abc

### Shallow routing

shallow routing은 data fetching method를 실행시키지 않고도 url을 변경할 수 있게 해줌. (`shallow: true`)

아래는 `?counter=10`으로 업데이트하는 예시

```
import { useEffect } from 'react'
import { useRouter } from 'next/router'

// Current URL is '/'
function Page() {
  const router = useRouter()

  useEffect(() => {
    // Always do navigations after the first render
    router.push('/?counter=10', undefined, { shallow: true })
  }, [])

  useEffect(() => {
    // The counter changed!
  }, [router.query.counter])
}

export default Page
```

url이 변경되는 것을 `componentDidUpdate`에서도 볼 수 있음.

```
componentDidUpdate(prevProps) {
  const { pathname, query } = this.props.router
  // verify props have changed to avoid an infinite loop
  if (query.counter !== prevProps.router.query.counter) {
    // fetch data based on the new query
  }
}
```

#### 주의사항

shallow routing은 같은 페이지 URL에서만 작동함. 새로운 페이지 url로 변경되면 해당 페이지가 로드되기 떄문.

그래서 아래와 같이는 쓸 수 없음.

```
router.push('/?counter=10', '/about?counter=10', { shallow: true })
```

## API Routes

next.js로 API를 만들수 있게 해줌. 굳이 쓸 일이 있을까?

dynamic API Routes, API Middleware 등도 제공하긴 하는데 나중에 필요할때 보면될듯.


## Authentication

authentication은 어떤 data-fetching 전략을 사용하는지에 따라서 달라질 수 있음.

- Use static generation to server-render a loading state, followed by fetching user data clinet-side
- Fetch user data server-side to eliminate a flash of unauthenticated content

### Authenticating Statically generated pages

next.js는 특별한 blocking data requirement가 없다면 (`getServerSideProps`, `getInitialProps` 등이 없다면) 해당 페이지를 static하다고 결정함.

대신 페이지가 server에서 loading state로 렌더한 이후, clinet-side에서 user를 fetching하면 됨.

장점은 페이지를 `next/link`를 사용해서 CDN으로 서빙 가능 -> fastser TTI (time to interactive)

아래는 이렇게 구현된 profile page.

```
// pages/profile.js

import useUser from '../lib/useUser'
import Layout from '../components/Layout'

const Profile = () => {
  // Fetch the user client-side
  const { user } = useUser({ redirectTo: '/login' })

  // Server-render loading state
  if (!user || user.isLoggedIn === false) {
    return <Layout>Loading...</Layout>
  }

  // Once the user request finishes, show the user
  return (
    <Layout>
      <h1>Your Profile</h1>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </Layout>
  )
}

export default Profile
```

### Authenticating Server-rendered page

`getServerSideProps`를 사용하면 next.js는 각 요청에 페이지를 pre-render하고 데이터를 props로 반환함.

```
export async function getServerSideProps(context) {
  return {
    props: {}, // Will be passed to the page component as props
  }
}
```

이전 profile page 예시를 server-side rendering으로 수정한 예시.

```
// pages/profile.js

import withSession from '../lib/session'
import Layout from '../components/Layout'

export const getServerSideProps = withSession(async function ({ req, res }) {
  // Get the user's session based on the request
  const user = req.session.get('user')

  if (!user) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    }
  }

  return {
    props: { user },
  }
})

const Profile = ({ user }) => {
  // Show the user. No loading state is required
  return (
    <Layout>
      <h1>Your Profile</h1>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </Layout>
  )
}

export default Profile
```

이 패턴의 장점은 redirect 되기 전 unauthenticated content에 대해서 생각할 필요가 없다는점.

대신 `getServerSideProps`에서 user를 가져오기 전까지 rendering이 블락되기 때문에 authentication lookup이 빨라야한다.

### Authentication providers

#### Own Database

- [with-iron-session](https://github.com/vercel/next.js/tree/canary/examples/with-iron-session)
  - low-level, encrypted, stateless session
- [next-auth](https://github.com/nextauthjs/next-auth-example)
  - full-featured authentication system with built-in providers (Google, FB, GH), JWT, email/password ...
  - 웬만하면 next-auth 쓸듯?

- [passport](http://www.passportjs.org/)라는것도 있다고함.

그 이외 firebase, [magic](https://magic.link/)(passwordless auth), Auth0, superbase 등등에 대한 예시가 잘 나와있음.


## [Advanced features](https://nextjs.org/docs/advanced-features/preview-mode)

이건 굳이 지금 읽을필요 없을거같고 뭐뭐 있는지정도 알고 있으면 좋을듯.

- preview mode: 말그대로 preview mode.
- dynamic import
  - ES2020의 [dynamic import](https://github.com/tc39/proposal-dynamic-import) 기능을 지원한다고함.
  - 뭔지 나중에 한번 보면 좋을듯.
- Automatic  static optimization
  - 어떻게 next.js가 페이지가 static인지 결정하는지
- Static HTML Export
  - `next export` -> app을 static HTML로 export 해줌 (node.js없는 standalone)
  - 이럴거면 굳이 next.js 쓸이유가..
- Absolute imports and module path aliases
  - 이건 실제로 사용하면 import가 훨씬 깔끔해질듯?

```
// tsconfig.json or jsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"]
    }
  }
}
```
```
// components/button.js
export default function Button() {
  return <button>Click me</button>
}
```
```
// pages/index.js
import Button from '@/components/button'

export default function HomePage() {
  return (
    <>
      <h1>Hello World</h1>
      <Button />
    </>
  )
}
```
- AMP Support
  - React page를 AMP page로 변경 가능
  - [AMP](https://amp.dev/)는 처음 들어봤는데 Accelerated Mobile Page라고 함.
- Customizing Babel config
- Customizing PostCSS config
- Custom server
  - `next start`로 시작되는 next.js의 node.js 서버를 custom 할수있음
  - custom server는 vercel에 배포할수 없다고함.
- Custom `App`
  - `./pages/_app.js`에서 `import App from 'next/app'`으로 import 해서 customize 할수있음
- Custom `Document`
  - html, body 태그 등을 변경하기 위해서 쓰임.
- Custom Error page
  - `pages/404.js`, `pages/500.js` 등
- `src` Directory
  - root `pages` 디렉토리 말고 `src/pages`처럼 쓰고싶은 사람들을 위해 넣어놓은듯
- Multi zones
  - zone: single deployment of a next.js app
  - 여러 zone을 한개의 앱으로 합칠 수 있따고함 (?)
- Measuring performance 
  - vercel로 배포하면 이것저것 제공한다고함
- [Debugging](https://nextjs.org/docs/advanced-features/debugging)
  - chrome devtools, vscode debugger
  - next.js debug mode로 실행시킬 수 있음 (`--inspect` flag)
  - 그 이후 debugger에 연결 (`chrome://inspect`)
  - [debugger](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/debugger) statement를 frontend/backend 코드에 모두 사용 가능. breakponit처럼 사용 가능
  - 이부분은 한번 따로 정리해서 실제로 사용해보면 좋을듯
- Source maps
- Codemods
- Internationalized routing
