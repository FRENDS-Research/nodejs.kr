## 타이머 (Timers)

### setTimeout(callback, delay, [arg], [...])

To schedule execution of `callback` after `delay` milliseconds. Returns a
`timeoutId` for possible use with `clearTimeout()`. Optionally, you can
also pass arguments to the callback.
`delay` 밀리초가 경과 후 `callback`이 실행되도록 예약한다. `clearTimeout()`에서 사용할
수 있는 `timeoutId`를 반환한다. 옵션으로 콜백에 인자를 전달할 수 있다.

`delay` 밀리초후에 `callback`의 실행을 스케쥴링한다. 리턴해주는 `timeoutId`을 `clearTimeout()`에서 사용할 수 있다. 옵션사항으로 콜백에 아규먼트를 전달할 수 있다.

### clearTimeout(timeoutId)

Prevents a timeout from triggering.
타임아웃이 트리거를 방지한다.

### setInterval(callback, delay, [arg], [...])

To schedule the repeated execution of `callback` every `delay` milliseconds.
Returns a `intervalId` for possible use with `clearInterval()`. Optionally,
you can also pass arguments to the callback.
`delay` 밀리초 간격으로 `callback`이 반복 실행하도록 예약한다. `clearInterval()`에서 사용할
수 있는 intervalId를 반환한다. 옵션으로 콜백에 인자를 전달할 수 있다.

매 `delay` 밀리초마다 `callback`의 실행을 반복하도록 스케쥴링한다. 리턴해주는 `intervalId`는 `clearInterval()`에서 사용할 수 있다. 옵션사항으로 콜백에 아규먼트를 전달할 수 있다.

### clearInterval(intervalId)

Stops a interval from triggering.
<<<<<<< HEAD
interval 이 트리거되는 것을 방지한다.
=======

인터벌의 실행을 멈춘다.
>>>>>>> 34b41aa058f13f6b6a36368950f424d0f0b6eec7
