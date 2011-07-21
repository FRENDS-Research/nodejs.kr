## Query String

This module provides utilities for dealing with query strings.
It provides the following methods:
이 모듈은 쿼리 스트링을 다루기 위한 유용한 것들을 제공한다.  다음과 같은 메소드들을 제공한다.

### querystring.stringify(obj, sep='&', eq='=')

Serialize an object to a query string.
Optionally override the default separator and assignment characters.
쿼리 객체를 문자열로 직렬화한다. 선택적으로 기본 구분 문자를 오버라이드 한다.

Example:

    querystring.stringify({foo: 'bar'})
    // returns
    'foo=bar'

    querystring.stringify({foo: 'bar', baz: 'bob'}, ';', ':')
    // returns
    'foo:bar;baz:bob'

### querystring.parse(str, sep='&', eq='=')

Deserialize a query string to an object.
Optionally override the default separator and assignment characters.
쿼리 문자열을 객체로 복원한다.  선택적으로 기본 구분 문자를 오버라이드 한다.

Example:

    querystring.parse('a=b&b=c')
    // returns
    { a: 'b', b: 'c' }

### querystring.escape

The escape function used by `querystring.stringify`,
provided so that it could be overridden if necessary.
escape 함수는 `querystring.stringify`에서 사용되고 있으며  필요에 따라 재정의 할 수 있도록 제공한다.

### querystring.unescape

The unescape function used by `querystring.parse`,
provided so that it could be overridden if necessary.
unescape 함수는 `querystring.parse` 에서 사용되고 있으며 필요에 따라 재정의 할 수 있도록 제공한다.
