## Timers

### setTimeout(callback, delay, [arg], [...])

To schedule execution of `callback` after `delay` milliseconds. Returns a
`timeoutId` for possible use with `clearTimeout()`. Optionally, you can
also pass arguments to the callback.
`delay` 밀리초가 경과 후 `callback`이 실행되도록 예약한다. `clearTimeout()`에서 사용할
수 있는 `timeoutId`를 반환한다. 옵션으로 콜백에 인자를 전달할 수 있다.

### clearTimeout(timeoutId)

Prevents a timeout from triggering.
타임아웃이 트리거되는 것을 방지한다.

### setInterval(callback, delay, [arg], [...])

To schedule the repeated execution of `callback` every `delay` milliseconds.
Returns a `intervalId` for possible use with `clearInterval()`. Optionally,
you can also pass arguments to the callback.
`delay` 밀리초 간격으로 `callback`이 반복 실행하도록 예약한다. `clearInterval()`에서 사용할
수 있는 intervalId를 반환한다. 옵션으로 콜백에 인자를 전달할 수 있다.

### clearInterval(intervalId)

Stops a interval from triggering.
interval 이 트리거되는 것을 방지한다.
