# Next.js

## 1. Next.js란?

Next.js는 React의 SSR(Server Side Rendering)을 쉽게 구현할 수 있게 도와주는 간단한 프레임워크 입니다.

리액트로 개발할 때 SPA를 이용하며 CSR을 하기 때문에 좋은 점도 있지만, 검색엔진 최적화 부분에서 단점을 가지고 있는데, CSR을 하게 되면 첫페이지에서 html 빈 페이지를 가져와 JS파일을 해석 후 화면을 구성하기 때문에 포털 검색에 거의 노출 될 일이 없어지게 됩니다.

하지만 Next.js 에서는 Pre-Rendering을 통해 페이지를 미리 렌더링 하며 완성된 html을 보여주기 때문에 검색엔진 최적화에도 유리하고, 초기 로딩 속도도 빠르기 때문에 사용자 경험도 좋아집니다.

### Server Side Rendering (SSR)

SSR은 클라이언트 대신 서버에서 페이지를 준비하는 원리입니다.
리액트에서는 클라이언트 사이드 렌더링이 진행되기 때문에 서버에 영향을 미치지 않고, 서버에서 클라이언트로 응답해서 보낸 html도 거의 비어있습니다. 이렇게 되면 검색엔진 최적화에도 좋지 않고, 초기 로딩 속도도 느려집니다.
그리고, 검색 엔진에 검색 시 웹크롤링이 동작할 때 내용을 제대로 수집하지 못합니다.


### Data Fetching

1. getStaticProps

Static Generation으로 빌드할 때 데이터를 불러옵니다. (미리 만듬)
언제 getStaticProps 를 사용해서 데이터를 불러올까요? 페이지가 빈번하게 업데이트 되지 않는 경우에 사용합니다.
그렇기 때문에, 페이지가 요청되기 전에 미리 빌드를 하고 빌드된 페이지를 사용자에게 제공함으로써 빠른 페이지 로딩 속도를 제공합니다.

[getStaticProps를 사용해야 할 때]

1. 페이지를 렌더링하는 데 필요한 데이터는 사용자의 요청보다 먼저 build 시간에 필요한 데이터를 가져올 때
2. 데이터는 Headless CMS에서 가져올 때
3. 데이터를 공개적으로 캐시할 수 있을 때
4. 페이지는 미리 렌더링 되어야 하고 매우 빨라야 할 때, (getStaticProps는 성능을 위해 CDN에서 캐시할 수 있는 HTML 및 JSON 파일을 생성합니다.)

```ts

const Blog = ({ posts }) => {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export const getStaticProps: GetStaticProps = async () => {
  const res = await fetch('https://.../posts')
  const posts = await res.json()
  return {
    props: {
      posts,
    },
  }
}

export default Blog

```

2. getStaticPaths

Static Generation으로 데이터에 기반하여 pre-render시 특정한 동적 라우팅을 구현합니다. (/pages/posts/[id].tsx)
언제 getStaticPaths 를 사용해서 데이터를 불러올까요? 페이지가 빈번하게 업데이트 되지 않는 경우에 사용합니다.
그렇기 때문에 getStaticProps와 마찬가지로, 페이지가 요청되기 전에 미리 빌드를 하고 빌드된 페이지를 사용자에게 제공함으로써 빠른 페이지 로딩 속도를 제공합니다.

```ts
getStaticPaths: GetStaticPaths = async () => {
  const res = await fetch('https://.../posts')
  const posts = await res.json()
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))
  return { paths, fallback: false }
}
```




3. getServerSideProps

Server Side Rendering으로 요청이 있을 때마다 데이터를 불러옵니다. 
언제 getServerSideProps 를 사용해서 데이터를 불러올까요? 페이지가 빈번하게 업데이트 되는 경우에 사용합니다.
그렇기 때문에, 페이지가 요청되기 전에 빌드를 하지 않고 요청이 있을 때마다 빌드를 하고 빌드된 페이지를 사용자에게 제공함으로써 항상 최신 데이터를 제공합니다.


```ts
getServerSideProps: GetServerSideProps = async () => {
  const res = await fetch('https://.../posts')
  const posts = await res.json()
  return {
    props: {
      posts,
    },
  }
}
```


