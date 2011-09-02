## URL

This module has utilities for URL resolution and parsing.
Call `require('url')` to use it.
이 모듈은 URL 확인 및 분석을 위한 유용한 기능들을 가졌다.
사용하려면 `require('url')` 를 호출해야 한다.

Parsed URL objects have some or all of the following fields, depending on
whether or not they exist in the URL string. Any parts that are not in the URL
string will not be in the parsed object. Examples are shown for the URL
분석된 URL 객체는 URL 문자열에 존재 여부에 따라 다음과 같은 필드를 일부 또는 전체를
갖는다.  URL 문자열에 포함되지 않는 필드는 분석 결과 객체에 포함되지 않는다.
다음과 같은 URL이 있다.

`'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

* `href`: The full URL that was originally parsed.
  Example: `'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

* `href`: 분석하기 전의 전체 URL 이다.
  예 : `'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

* `protocol`: The request protocol.
  Example: `'http:'`

* 요청 프로토콜.
  예: `'http:'`

* `host`: The full host portion of the URL, including port and authentication information.
  Example: `'user:pass@host.com:8080'`

* `host`: URL 전체 호스트 정보. 인증 정보를 포함한다.
  예: `'user:pass@host.com:8080'`

* `auth`: The authentication information portion of a URL.
  Example: `'user:pass'`

* `auth`:  URL 인증정보.
  예: `'user:pass'`

* `hostname`: Just the hostname portion of the host.
  Example: `'host.com'`

* `hostname`: 호스트 정보 중에 호스트 이름.
  예:`'host.com'`

* `port`: The port number portion of the host.
  Example: `'8080'`

* `port`: 호스트 정보 중에 포트번호이다.
예: `'8080'`

* `pathname`: The path section of the URL, that comes after the host and before the query, including the initial slash if present.
  Example: `'/p/a/t/h'`

* `pathname`: URL의 경로 부분. 호스트 정보에서 검색어 사이에 위치하고 있으며, 처음에 슬래시가 있으면 그것도 포함된다.
  예: `'/p/a/t/h'`

* `search`: The 'query string' portion of the URL, including the leading question mark.
  Example: `'?query=string'`

* `search`: URL 의 `query string` 이고 첫 물음표도 포함된다.
  예: `'?query=string'`

* `query`: Either the 'params' portion of the query string, or a querystring-parsed object.
  Example: `'query=string'` or `{'query':'string'}`

* `query`: 쿼리 변수 부분의 문자열이거나 쿼리 문자열 구문을 구분하는 개체이다.
  예: `'query=string'` 혹은 `{'query':'string'}`

* `hash`: The 'fragment' portion of the URL including the pound-sign.
  Example: `'#hash'`

* `hash`: URL의 # 표시를 포함하는 부분.
  예: `'#hash'`

The following methods are provided by the URL module:
다음 메서드드은 URL 모듈에서 제공된다.

### url.parse(urlStr, parseQueryString=false)

Take a URL string, and return an object.  Pass `true` as the second argument to also parse
the query string using the `querystring` module.
URL 문자열을 인수로 해석한 결과를 반환한다. `querystring` 모듈을 이용하여 쿼리 문자열을 구문 분석하려면
두번째 인수에 `true`를 전달한다.

### url.format(urlObj)

Take a parsed URL object, and return a formatted URL string.
URL 객체를 인수로 포맷화하는 URL 문자열을 반환한다.

### url.resolve(from, to)

Take a base URL, and a href URL, and resolve them as a browser would for an anchor tag.
기본이 되는 URL과 상대 URL을 브러우저에서 앵커 태그가 수행하는 것과 같이 URL을 확인한다.
