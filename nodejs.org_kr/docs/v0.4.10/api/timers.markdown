## 타이머 (Timers)

### setTimeout(callback, delay, [arg], [...])

To schedule execution of `callback` after `delay` milliseconds. Returns a
`timeoutId` for possible use with `clearTimeout()`. Optionally, you can
also pass arguments to the callback.

`delay` 밀리초후에 `callback`의 실행을 스케쥴링한다. 리턴해주는 `timeoutId`을 `clearTimeout()`에서 사용할 수 있다. 옵션사항으로 콜백에 아규먼트를 전달할 수 있다.

### clearTimeout(timeoutId)

Prevents a timeout from triggering.

타임아웃의 실행을 막는다.

### setInterval(callback, delay, [arg], [...])

To schedule the repeated execution of `callback` every `delay` milliseconds.
Returns a `intervalId` for possible use with `clearInterval()`. Optionally,
you can also pass arguments to the callback.

매 `delay` 밀리초마다 `callback`의 실행을 반복하도록 스케쥴링한다. 리턴해주는 `intervalId`는 `clearInterval()`에서 사용할 수 있다. 옵션사항으로 콜백에 아규먼트를 전달할 수 있다.

### clearInterval(intervalId)

Stops a interval from triggering.

인터벌의 실행을 멈춘다.
