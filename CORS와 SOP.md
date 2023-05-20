CORS에 대해 알아보기 전, 먼저 SOP를 알아봐야 한다. CORS란 결국 SOP가 지켜지지 않았을 때 발생하는 오류이기 때문이다.

# SOP (Same-Origin-Policy)
SOP란 말 그대로 동일한 출처에서만 리소스를 공유할 수 있다는 규칙을 가진 정책이다. 두 URL간의 Scheme, Host, Port중 하나라도 다르면 SOP를 위반한 것이 된다. 

아래 예시로 같은 Origin인지 조금 더 알아보자
나의 블로그 출처인 `https://minju-k.tistory.com/`와 출처를 한번 비교해보자.
| URL                               | 결과 | 이유                           |
| --------------------------------- | ---- | ------------------------------ |
| http://minju-k.tistory.com/       | 실패 | 프로토콜 다름                  |
| https://minju-k.tistory.com:81/   | 실패 | 포트 다름( http는 80이 기본값) |
| https://minju.tistory.com/        | 실패 | host 다름                      |
| https://minju-k.tistory.com/about | 성공 | 경로만 다름                    |


# CORS (Cross-Origin Resource Sharing)
CORS는 교차 출처 리소스 공유라고 해석할 수 있는데, 교차 출처라는 것은 결국 `같은 출처`가 아니라는 것이다. 앞서 살펴본 SOP에 해당되는 게 아니면 교차 출처라고 보는 것이고, 이렇게 SOP 가 아닌 곳에서 자원을 받아오는 것을 막아주는게 CORS정책이라고 할 수 있다.

여기서 기억해야 할 사실은 이렇게 출처를 비교하는 로직이 서버에 구현된 스펙이 아니라 브라우저에 구현되어 있는 스펙이라는 것이다. 만약 우리가 CORS 정책을 위반하는 리소스를 요청하더라도, 해당 서버가 같은 출처에서 보낸 요청만 받겠다는 로직을 가지고 있는 경우가 아니라면 서버는 정상적으로 응답을 하고, 이후에 브라우저가 서버가 보낸 응답을 분석해서 CORS 정책 위반이라고 판단되면 그 응답을 사용하지 않고 버리는 식으로 작동한다.

CORS는 브라우저 정책이기 때문에, 서버 간 통신할 때에는 이 정책이 적용되지 않으며, CORS 정책을 위반하는 리소스 요청 때문에 에러가 발생했다고 해도 서버 쪽 로그에는 정상적으로 응답했다는 로그만 남는다는 것을 기억해야 한다.

# CORS의 동작방식
1. 기본적으로 클라이언트에서 서버에 다른 출처의 리소스를 요청할 때에는 HTTP 프로토콜을 사용하여 요청을 보내는데, 이  때 브라우저는 요청 헤더의 `Origin`이라는 필등 요청을 보내는 출처를 함께 담아 보낸다.

2. 이후 서버가 요청에 대한 응답을 할 때, 응답 헤더의 `Access-Control-Allow-Origin`이라는 값에 이 리소스를 접근하는 것이 허용된 출처를 내려주면

3. 브라우저는 자신이 보냈던 요청의 `Origin`값과 서버가 보내준 `Access-Control-Allow-Origin`을 비교한 후 응답이 유효한 응답이라면 사용, 아니라면 CORS에러를 출력하는 것이다.

기본적인 흐름은 위와 같고, CORS가 동작하는 방식은 다음과 같이 세 가지의 시나리오에 따라 변경된다.

## 1. Preflight Request
프리플라이트 방식은 우리가 일반적으로 개발할 때 가장 많이 마주치는 시나리오이다.  이 시나리오는 본 요청에 앞서 HTTP OPTIONS라는 메서드를 통해 브라우저 스스로 이 요청을 보내는 것이 안전한지 확인하는 절차인데 이를 Preflight 요청이라고 한다.

mdn에서 가져온 예제로 한번 살펴보자.
내가 다음과 같이 POST로 어떤 요청을 보낸다고 해보자. Content-type이 application/xml이고, 사용자 정의 헤더가 설정되었기 때문에 이 요청은 브라우저에 의해 preflighted 처리가 된다.
```js
// preflight 요청 예제
const xhr = new XMLHttpRequest();
xhr.open('POST', 'https://bar.other/resources/post-here/');
xhr.setRequestHeader('Ping-Other', 'pingpong');
xhr.setRequestHeader('Content-Type', 'application/xml');
xhr.onreadystatechange = handler;
xhr.send('<person><name>Arun</name></person>');
```
![[Pasted image 20230320004928.png]]

위 요청을 살펴보면 본 요청에 앞서 Preflight request가 일어난 것을 볼 수 있다. OPTIONS 요청으로 Origin을 전달했고, 이에 대한 응답으로 서버는 Access-Control-Allow-Origin을 통해 이 리소스에 접근이 가능한 출처가 https:/foo.example이라고 알려주었다. 요청을 보낸 Origin과 서버가 응답으로 준 출처가 같으므로 CORS 정책을 위반하지 않았기 때문에 정상적으로 본 요청이 진행될 수 있다.

만약 여기서 서버가 내려준 Access-Control-Allow-Origin값이 https://minju-k.com이라고 했더라도 응답을 확인해보면 정상적으로 200 OK가 났을 것이고, 하지만 CORS에러가 났을 것이다. 실제로 브라우저가 출처를 확인하는 과정은 서버에서 응답을 받은 후 이기 때문에, 중요한 것은 예비 요청의 성공/실패 여부가 아니라, 응답 헤더에 유효한 Access-Control-Allow-Origin 값이 존재하는 가이다.

## 2. Simple Request
다음과 같은 조건을 만족하는 일부 요청은 CORS preflight가 일어나지 않고 바로 본 요청을 하는 Simple Request만 일어난다.

1.  요청의 메소드는 `GET`, `HEAD`, `POST` 중 하나여야 한다.
2.  `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, `DPR`, `Downlink`, `Save-Data`, `Viewport-Width`, `Width`를 제외한 헤더를 사용하면 안된다.
3.  만약 `Content-Type`를 사용하는 경우에는 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`만 허용된다.

위와 같은 경우에는 바로 요청을 하며 Origin을 보내면, 서버는 이에 대한 응답으로 Access-Control-Allow-Origin 값을 내려준다.


## 3. Credentialed Request
세 번째 시나리오는 인증된 요청을 사용하는 방법으로, 이 시나리오는 CORS의 기본적인 방식이라기 보다는 다른 출처 간 통신에서 보안을 더 강화하고 싶을 때 사용하는 방법이다.

기본적인 XMLHttpRequest 객체나 fetch API는 별도의 옵션 없이 브라우저의 쿠키 정보나 인증과 관련된 헤더를 함부로 요청에 담지 않는데, 이 때 요청에 인증과 관련된 정보를 담을 수 있게 해주는 옵션이 바로 `credentials` 옵션이다.

이 옵션에는 아래 3 가지 값을 사용할 수 있다.
| 옵션 값              | 설명                                            |
| -------------------- | ----------------------------------------------- |
| same-origin(default) | 같은 출처 간 요청에만 인증 정보를 담을 수 있다. |
| include              | 모든 요청에 인증 정보를 담을 수 있다.           |
| omit                 | 모든 요청에 인증 정보를 담지 않는다.            |

만약 내가 same-origin이나 include와 같은 옵션을 사용하여 리소스 요청에 인증정보가 포함된다면, 브라우저는 출처를 확인할 때 Access-Control-Allow-Origin 외에 추가로 더 검사 조건을 가져가 확인하게 된다.

그 두 가지 조건은 아래와 같다.
1.  `Access-Control-Allow-Origin`에는 `*`를 사용할 수 없으며, 명시적인 URL이어야한다.
2.  응답 헤더에는 반드시 `Access-Control-Allow-Credentials: true`가 존재해야한다.

따라서 만약 내가 아래 처럼 credentials 필드에 include 값을 넣어 보냈다면, 서버에 와일드 카드가 되어 있어도 CORS 에러가 나며, Access-Control-Allow-Credentials값이 없다면 CORS 에러가 난다는 것이다.
```js
fetch('https://minju-k.com/feed.xml', {
  credentials: 'include', // Credentials 옵션 변경!
});
```

# CORS 에러 해결하기
CORS 에러가 무엇인지 확인해 보았으니, 해결하는 방법을 알아야 하겠다.

## Access-Control-Allow-Origin
첫 번째 방법은 서버의 Access-Control-Allow-Origin에 내가 요청하는 Origin을 넣어주는 방법이다. Origin과 해당 값이 다를 때에 CORS가 발생하므로 이 값을 세팅하면 CORS 에러가 해결되는 것은 너무도 당연한 것 같다. 

`*` 와일드 카드를 사용하여 모든 출처에서 오는 요청을 다 받겠다고 설정할 수도 있겠지만, 이렇게 설정할 경우 이상한 출처에서 오는 요청도 다 받게 되므로 보안적인 이슈가 발생할 수도 있기에 URL을 명시해 주는 것이 좋은 방식이다.

## Webpack Dev Server로 리버스 프록싱하기
프론트엔드 환경에서 개발하다보면 서버에서 `http://localhost:3000`과 같은 범용적 출처를 넣어주는 경우가 드물기 때문에 CORS 에러를 마주칠 확률이 높다. 이럴 때에, 서버에 해당 주소를 헤더의 Access-Control-Allow-Origin에 넣어달라고 하는 것 외에 프론트엔드 단에서 해결할 수 있는 방법은 webpack-dev-server가 제공하는 프록시 기능을 이용하는 것이다.

```js
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'https://api.minju-k.com',
        changeOrigin: true,
        pathRewrite: { '^/api': '' },
      },
    }
  }
}
```

위와 같이 설정하게 되면 `/api`로 시작하는 URL로 보내는 요청에 대해 브라우저는 `localhost:8000/api`로 요청을 보낸 것으로 알고 있지만, 웹팩이 `https://api.minju-k.com`으로 요청을 프록싱해주기 때문에 CORS 정책을 지킨 것처럼 브라우저를 속이면서도 우리가 원하는 서버와 통신을 자유롭게 할 수 있다.

다만, 이 방법은 실제 프로덕션 환경에는 적용되지 않는다. 로컬 개발 환경에서는 웹팩이 요청을 프록시해주지만 실제 프로젝트를 빌드하고 서버에 올리면 더이상 webpack-dev-server가 구동하는 환경이 아니기 때문에 주의해야 한다. 

배포할 때, API가 도메인에서 제공이 되는 경우에는 문제가 되지 않지만, API의 도메인과 서비스의 도메인이 다르다면 axios의 글로벌 [baseURL](https://github.com/axios/axios#global-axios-defaults) 을 설정해주면 된다. 

```javascript
axios.defaults.baseURL = process.env.NODE_ENV === 'development' ? '/' : '{API 도메인}';
```
이렇게 해주면 개발환경에서는 프록시 서버로 요청하고, 실제 프로덕션에서는 실제 API 서버로 요청을 하게 된다.

