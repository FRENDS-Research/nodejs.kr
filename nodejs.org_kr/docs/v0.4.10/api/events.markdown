## Events

Many objects in Node emit events: a `net.Server` emits an event each time
a peer connects to it, a `fs.readStream` emits an event when the file is
opened. All objects which emit events are instances of `events.EventEmitter`.
You can access this module by doing: `require("events");`

노드에서 많은 객체들은 이벤트를 발생한다. `net.Server`는 피어가 연결될 때마다 이벤트를 발생시키고 `fs.readStream`는 파일이 열렸을 때 이벤트를 발생시킨다. 이벤트를 발생시키는 모든 오브젝트는 `events.EventEmitter`의 인스턴스이다. `require("events");`를 통해서 이 모듈에 접근할 수 있다.

Typically, event names are represented by a camel-cased string, however,
there aren't any strict restrictions on that, as any string will be accepted.

일반적으로 이벤트명은 카멜케이스 문자열로 표현되지만 어떤 엄격한 제한은 없어서 어떤 문자열도 가능하다.

Functions can then be attached to objects, to be executed when an event
is emitted. These functions are called _listeners_.

이벤트가 발생했을 때 실행되로록 함수는 객체에 덧붙혀질 수 있다.  이러한 함수들을 _리스너_라고 부른다.

### events.EventEmitter

To access the EventEmitter class, `require('events').EventEmitter`.

EventEmitter 클래스에 접근하기 위해  `require('events').EventEmitter`를 사용한다.

When an `EventEmitter` instance experiences an error, the typical action is
to emit an `'error'` event.  Error events are treated as a special case in node.
If there is no listener for it, then the default action is to print a stack
trace and exit the program.

`EventEmitter` 인스턴스가 에러를 만났을 때 일반적인 행동은 `'error'` 이벤트를 발생시키는 것이다. 에러 이벤트는 노드에서 특별한 경우로 취급된다. 에러 이벤트를 위한 리스너가 없으면 기본 동작은 스택트레이스를 출력하고 프로그램을 종료하는 것이다.

All EventEmitters emit the event `'newListener'` when new listeners are
added.

모든 EventEmitter는 새로운 리스너가 추가되었을 때 `'newListener'`이벤트를 발생시킨다. 

#### emitter.addListener(event, listener)
#### emitter.on(event, listener)

Adds a listener to the end of the listeners array for the specified event.

리스너 배열의 끝에 명시된 이벤트에 대한 리스너를 추가한다.

    server.on('connection', function (stream) {
      console.log('someone connected!');
    });

#### emitter.once(event, listener)

Adds a **one time** listener for the event. The listener is
invoked only the first time the event is fired, after which
it is removed.

이벤트에 **1회성** 리스너를 추가한다. 리스너는 처음 이벤트가 발생했을 때만 호출되고 그 다음 제거된다. 

    server.once('connection', function (stream) {
      console.log('Ah, we have our first user!');
    });

#### emitter.removeListener(event, listener)

Remove a listener from the listener array for the specified event.
**Caution**: changes array indices in the listener array behind the listener.

명시된 이벤트에 대한 리스너를 리스너 배열에서 제거한다. **주의**: 리스너배열에서 삭제한 리스너 뒤에 오는 배열 인덱스들을 변경한다.

    var callback = function(stream) {
      console.log('someone connected!');
    };
    server.on('connection', callback);
    // ...
    server.removeListener('connection', callback);


#### emitter.removeAllListeners(event)

Removes all listeners from the listener array for the specified event.

리스너 배열에서 명시된 이벤트에 대한 모든 리스터를 제거한다.


#### emitter.setMaxListeners(n)

By default EventEmitters will print a warning if more than 10 listeners are
added to it. This is a useful default which helps finding memory leaks.
Obviously not all Emitters should be limited to 10. This function allows
that to be increased. Set to zero for unlimited.

10개가 넘는 리스터가 추가되었을 때 기본 EventEmitter는 경고를 출력할 것이다. 이것은 메모리누수를 찾를 것을 돕는 기본값이다. 명백하게 모든 EMITTER가 10개의 제한되어야 하는 것은 아니다. 이 함수는 증가될 수 있다. 0부터 무제한까지 설정한다. 


#### emitter.listeners(event)

Returns an array of listeners for the specified event. This array can be
manipulated, e.g. to remove listeners.

명시된 이벤트에 대한 리스너의 배열을 리턴한다. 이 배열은 조작될 수 있다. 예를 들면 리스너들을 삭제한다.

    server.on('connection', function (stream) {
      console.log('someone connected!');
    });
    console.log(util.inspect(server.listeners('connection')); // [ [Function] ]

#### emitter.emit(event, [arg1], [arg2], [...])

Execute each of the listeners in order with the supplied arguments.

제공된 아규먼트들과 순서대로 리스너들을 각각 실행한다.

#### Event: 'newListener'

`function (event, listener) { }`

This event is emitted any time someone adds a new listener.

이 이벤트는 새로운 리스너가 추가될 때마다 발생한다.
