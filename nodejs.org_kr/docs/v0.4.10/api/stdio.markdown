## console

Browser-like object for printing to stdout and stderr.

브라우저처럼 stdout과stderr을 출력해주는 객체.

### console.log()

Prints to stdout with newline. This function can take multiple arguments in a
`printf()`-like way. Example:

새로운 라인에 stdout을 출력한다. 이 함수는 `printf()`처럼 여러가지 아규먼트를 가질수 있다. 예제: 

    console.log('count: %d', count);

If formating elements are not found in the first string then `util.inspect`
is used on each argument.

첫 문자열에서 포맷팅 요소가 발견되지 않으면 `util.inspect`가 각 아규먼트에서 사용된다.

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

