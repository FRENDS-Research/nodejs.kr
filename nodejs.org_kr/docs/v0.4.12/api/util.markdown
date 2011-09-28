## util

These functions are in the module `'util'`. Use `require('util')` to access
them.

이 함수들은 `'util'`모듈안에 있으며 `require('util')`를 사용해서 접근할 수 있다.


### util.debug(string)

A synchronous output function. Will block the process and
output `string` immediately to `stderr`.

동기적인 출력 함수. 프로세스를 블락킹하고 `string`을 `stderr`에 즉시 출력한다.

    require('util').debug('message on stderr');


### util.log(string)

Output with timestamp on `stdout`.

`stdout`에 타임스탬프와 함게 출력한다.

    require('util').log('Timestamped message.');


### util.inspect(object, showHidden=false, depth=2)

Return a string representation of `object`, which is useful for debugging.

디버깅하는에 유용한 `object`의 문자열 표현을 리턴한다.

If `showHidden` is `true`, then the object's non-enumerable properties will be
shown too.

`showHidden`이 `true`이면 객체의 열거적이지 않은 프로퍼티들도 보여준다.

If `depth` is provided, it tells `inspect` how many times to recurse while
formatting the object. This is useful for inspecting large complicated objects.

`depth`가 제공되면 `inspect`가 객체를 포매팅하면서 얼마나 많이 재귀할 것인지를 지정한다. 크고 복잡한 객체를 검사할때 유용하다.

The default is to only recurse twice.  To make it recurse indefinitely, pass
in `null` for `depth`.

기본은 오직 2번만 재귀하는 것이다. 무한하게 재귀하려면 `depth`에 `null`을 전달해라.

Example of inspecting all properties of the `util` object:

`util`객체의 모든 프로퍼티들을 검사하는 예제:

    var util = require('util');

    console.log(util.inspect(util, true, null));


### util.pump(readableStream, writableStream, [callback])

Experimental

Read the data from `readableStream` and send it to the `writableStream`.
When `writableStream.write(data)` returns `false` `readableStream` will be
paused until the `drain` event occurs on the `writableStream`. `callback` gets
an error as its only argument and is called when `writableStream` is closed or
when an error occurs.

실험적인

`readableStream`에서 데이터를 읽어서 `writableStream`으로 보낸다. `writableStream.write(data)`이 `false`를 리턴하면 `readableStream`는 `writableStream`에서 `drain`이벤트가 발생할 때 까지 멈춘다. `callback`은 유일한 아규먼트인 error를 취하고 `writableStream`이 닫히거나 에러가 발생했을 때 호출된다.


### util.inherits(constructor, superConstructor)

Inherit the prototype methods from one
[constructor](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/constructor)
into another.  The prototype of `constructor` will be set to a new
object created from `superConstructor`.

한 [constructor](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/constructor)에서 다른 것으로 프로토타입 메서드들을 상속한다. `constructor`의 프로토타입은 `superConstructor`에서 생성된 새로운  객체에 설정될 것이다. 

As an additional convenience, `superConstructor` will be accessible
through the `constructor.super_` property.

추가적인 편리함으로 `superConstructor`는 `constructor.super_` 프로퍼티를 통해서 접근가능할 것이다.

    var util = require("util");
    var events = require("events");

    function MyStream() {
        events.EventEmitter.call(this);
    }

    util.inherits(MyStream, events.EventEmitter);

    MyStream.prototype.write = function(data) {
        this.emit("data", data);
    }

    var stream = new MyStream();

    console.log(stream instanceof events.EventEmitter); // true
    console.log(MyStream.super_ === events.EventEmitter); // true

    stream.on("data", function(data) {
        console.log('Received data: "' + data + '"');
    })
    stream.write("It works!"); // Received data: "It works!"
