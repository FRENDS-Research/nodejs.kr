## console

Browser-like object for printing to stdout and stderr.

브라우저처럼 stdout과stderr을 출력해주는 객체.

### console.log()

Prints to stdout with newline. This function can take multiple arguments in a
`printf()`-like way. Example:

새로운 라인에 stdout을 출력한다. 이 함수는 `printf()`처럼 여러가지 아규먼트를 가질수 있다. 예제: 

    console.log('count: %d', count);

The first argument is a string that contains zero or more *placeholders*.
Each placeholder is replaced with the converted value from its corresponding
argument. Supported placeholders are:

첫 아규먼트는 하나이상의 *플레이스홀더*를 담고 있는 문자열이다.
각 플레이스홀더는 대응되는 아규먼트의 변환된 값으로 교체된다.
지원되는 플레이스홀더는 다음과 같다:

* `%s` - String.
* `%d` - Number (both integer and float).
* `%j` - JSON.

If the placeholder does not have a corresponding argument, `undefined` is used.

플레이스홀더에 대응되는 아규먼트가 없다면 `undefined`를 사용한다.

    console.log('%s:%s', 'foo'); // 'foo:undefined'

If there are more arguments than placeholders, the extra arguments are
converted to strings with `util.inspect()` and these strings are concatenated,
delimited by a space.

플레이스홀더보다 많은 아규먼트가 있을 경우 여분의 아규먼트는
`util.inspect()`로 문자열로 변환되고 각 문자열은 스페이스로 구분되어 연결된다.

    console.log('%s:%s', 'foo', 'bar', 'baz'); // 'foo:bar baz'

If the first argument is not a format string then `console.log()` prints
a string that is the concatenation of all its arguments separated by spaces.
Each argument is converted to a string with `util.inspect()`.

첫 아규먼트가 문자열 형식이 아니면 `console.log()`는 모든 아규먼트를
스페이스로 구분해서 연결한 문자열을 출력한다.
각 아규먼트는 `util.inspect()`를 통해서 문자열로 변환된다.

    console.log(1, 2, 3); // '1 2 3'


### console.info()

Same as `console.log`.

`console.log`와 동일하다.

### console.warn()
### console.error()

Same as `console.log` but prints to stderr.

`console.log`와 동일하지만 stderr을 출력한다.

### console.dir(obj)

Uses `util.inspect` on `obj` and prints resulting string to stderr.

`obj`에서 `util.inspect`를 사용하고 stderr에 결과 문자열을 출력한다.

### console.time(label)

Mark a time.

시간을 기록한다.

### console.timeEnd(label)

Finish timer, record output. Example

타이머를 종료하고 결과를 기록한다. 예제

    console.time('100-elements');
    for (var i = 0; i < 100; i++) {
      ;
    }
    console.timeEnd('100-elements');


### console.trace()

Print a stack trace to stderr of the current position.

현재 위치의 stderr의 스택트레이스를 출력한다.

### console.assert()

Same as `assert.ok()`.

`assert.ok()`와 동일하다.

