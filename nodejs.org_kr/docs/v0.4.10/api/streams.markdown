## 스트림 (Streams)

A stream is an abstract interface implemented by various objects in Node.
For example a request to an HTTP server is a stream, as is stdout. Streams
are readable, writable, or both. All streams are instances of `EventEmitter`.

스트림은 노트에서 여러가지 객체로 구현되는 추상인터페이스이다. 예를 들어 HTTP 서버에 대한 요청은 stdout이므로 스트림이다. 스트림은 읽을 수 있거나 쓸 수 있거나 혹은 둘다 가능하다. 모든 스트림은 `EventEmitter`의 인스턴스이다. 

## Readable Stream

## 읽을 수 있는 스트림

A `Readable Stream` has the following methods, members, and events.

`Readable Stream`은 아래의 메서드, 멤버, 이벤트를 가지고 있다.

### Event: 'data'

`function (data) { }`

The `'data'` event emits either a `Buffer` (by default) or a string if
`setEncoding()` was used.

`data` 이벤트는 `Buffer`(기본값)에서나 `setEncoding()`가 사용되었다면 문자열에서  발생한다. 

### Event: 'end'

`function () { }`

Emitted when the stream has received an EOF (FIN in TCP terminology).
Indicates that no more `'data'` events will happen. If the stream is also
writable, it may be possible to continue writing.

스트림이 EOF(TCP 기술에서는 FIN)를 받았을 때 발생한다. 더이상 `data`이벤트가 발생하지 않을 것이라는 것을 나타낸다. 스트림을 쓸 수도 있다면 계속해서 쓰는 것이 가능할 것이다.

### Event: 'error'

`function (exception) { }`

Emitted if there was an error receiving data.

데이터를 받는데 에러가 있다면 발생한다.

### Event: 'close'

`function () { }`

Emitted when the underlying file descriptor has been closed. Not all streams
will emit this.  (For example, an incoming HTTP request will not emit
`'close'`.)

기반이 되는 파일 디스크립터가 닫혔을때 발생한다. 모든 스트림이 이 이벤트를 발생하는 것은 아니다.(예를 들어 들어오는 HTTP dycjddms `'close'`를 발생하지 않을 것이다.)

### Event: 'fd'

`function (fd) { }`

Emitted when a file descriptor is received on the stream. Only UNIX streams
support this functionality; all others will simply never emit this event.

파일 디스크립터가 스트림에서 받아졌을 때 발생한다. UNIX 스트림만이 이 기능을 지원한다; 다른 곳에서는 이 이벤트가 절대 발생하지 않는다.

### stream.readable

A boolean that is `true` by default, but turns `false` after an `'error'`
occurred, the stream came to an `'end'`, or `destroy()` was called.

기본적으로 `true`이지만 `'error'`가 발생하거나 스트림이 `'end'`되거나 `destroy()`가 호출되었을 때 `false`로 바뀐다. 

### stream.setEncoding(encoding)
Makes the data event emit a string instead of a `Buffer`. `encoding` can be
`'utf8'`, `'ascii'`, or `'base64'`.

데이터 이벤트가 `Buffer`대신에 문자열을 발생시키도록 만든다. `encoding`은 `'utf8'`, `'ascii'`, `'base64'`가 될 수 있다.

### stream.pause()

Pauses the incoming `'data'` events.

들어오는 `'data'`이벤트를 멈춘다.

### stream.resume()

Resumes the incoming `'data'` events after a `pause()`.

`pause()`후에 들어오는 `'data'`이벤트를 다시 시작한다.

### stream.destroy()

Closes the underlying file descriptor. Stream will not emit any more events.

기반이되는 파일 디스크립터를 닫는다. 스트림은 더이상 이벤트를 발생시키지 않을 것이다.


### stream.destroySoon()

After the write queue is drained, close the file descriptor.

쓰기 큐(write queue)를 모두 소모시킨 후에 파일 디스크립터를 닫는다.


### stream.pipe(destination, [options])

This is a `Stream.prototype` method available on all `Stream`s.

모든 `Stream`에서 이용가능한 `Stream.prototype` 메서드이다.

Connects this read stream to `destination` WriteStream. Incoming
data on this stream gets written to `destination`. The destination and source
streams are kept in sync by pausing and resuming as necessary.

이 읽기 스트림을 `destination` WriteStream으로 연결한다. 이 스트림에서 들어오는 데이터는 `destination`으로 쓰여진다. 목적지 스트림과 소스 스트림은 필요에 따라 멈추거나 다시 시작함으로써 동기화를 유지한다.

Emulating the Unix `cat` command:

유닉스의 `cat` 명령어를 에뮬레이트한다.

    process.stdin.resume();
    process.stdin.pipe(process.stdout);


By default `end()` is called on the destination when the source stream emits
`end`, so that `destination` is no longer writable. Pass `{ end: false }` as
`options` to keep the destination stream open.

기본적으로 소스 스트림에서 `end`가 발생하면 목적지 스트림에서 `end()`가 호출되어서 `destination`이 더이상 쓸 수 없도록 만든다. 목적지 스트림을 열어놓은 상태로 유지하려면 `options`에 `{ end: false }`를 전달해라.

This keeps `process.stdout` open so that "Goodbye" can be written at the end.

마지막에 "Goodbye"가 쓰여질 수 있도록 `process.stdout`를 열려진 상태로 유지한다.

    process.stdin.resume();

    process.stdin.pipe(process.stdout, { end: false });

    process.stdin.on("end", function() {
      process.stdout.write("Goodbye\n");
    });

NOTE: If the source stream does not support `pause()` and `resume()`, this function
adds simple definitions which simply emit `'pause'` and `'resume'` events on
the source stream.

주의: 소스 스트림이 `pause()`와 `resume()`를 지원하지 않는 다면 이 함수는 소스 스트림에서 `'pause'`와 `'resume'` 이벤트를 간단히 발생하는 간단한 목적지 스트림을 추가할 것이다.

## Writable Stream

## 쓸 수 있는 스트림 (Writable Stream)

A `Writable Stream` has the following methods, members, and events.

`쓸 수 있는 스트림`은 아래의 메서드, 멤버, 이벤트들을 가지고 있다.

### Event: 'drain'

`function () { }`

Emitted after a `write()` method was called that returned `false` to
indicate that it is safe to write again.

다시 안정적으로 쓸 수 있다는 것을 나타내는 `false`가 리턴된 `write()`메서드가 호출된 후에 발생한다.

### Event: 'error'

`function (exception) { }`

Emitted on error with the exception `exception`.

`exception`예외와 함께 에러가 발생한다.

### Event: 'close'

`function () { }`

Emitted when the underlying file descriptor has been closed.

기반이 되는 파일 디스크립터가 닫혔을 때 발생한다.

### Event: 'pipe'

`function (src) { }`

Emitted when the stream is passed to a readable stream's pipe method.

읽을 수 있는 스트림의 pipe 메서드로 스트림이 전달되었을 때 발생한다.

### stream.writable

A boolean that is `true` by default, but turns `false` after an `'error'`
occurred or `end()` / `destroy()` was called.

기본적으로 `true`이지만 `'error'`가 발생하거나 `end()` / `destroy()`가 호출된 후에는 `false`로 바뀐다.

### stream.write(string, encoding='utf8', [fd])

Writes `string` with the given `encoding` to the stream.  Returns `true` if
the string has been flushed to the kernel buffer.  Returns `false` to
indicate that the kernel buffer is full, and the data will be sent out in
the future. The `'drain'` event will indicate when the kernel buffer is
empty again. The `encoding` defaults to `'utf8'`.

주어진 `encoding`으로 `string`을 스트림에 쓴다. 문자열이 커널 버퍼에 플러시(flush)되면 `true`를 리턴한다. 커널버퍼가 꽉 찼다는 의미로 `false`를 리턴하고 데이터는 나중에 보내질 것이다. `'drain'`이벤트는 커널버퍼가 다시 비워졌다는 것을 나타낼 것이다. `encoding`의 기본값은 `'utf8'`이다.

If the optional `fd` parameter is specified, it is interpreted as an integral
file descriptor to be sent over the stream. This is only supported for UNIX
streams, and is silently ignored otherwise. When writing a file descriptor in
this manner, closing the descriptor before the stream drains risks sending an
invalid (closed) FD.

옵션으로 `fd` 파라미터가 명시된다면 스트림으로 보내지기 위해서 완전한 파일 디스크립터로써 인터프리트된다. 이것은 오직 UNIX 스트림에서만 지원되고 다른 경우에는 아무런 경고 없이 무시된다. 이 경우에 파일 디스크립터를 쓰는 것은 스트림을 모두 빼기 전에 디스크립터를 닫아서 유효하지 않은(닫힌) FD에 보낼 위험이 있다. 

### stream.write(buffer)

Same as the above except with a raw buffer.

로우 버퍼외에는 앞의 것과 동일하다.

### stream.end()

Terminates the stream with EOF or FIN.

스트림이 EOF나 FIN으로 종료된다.

### stream.end(string, encoding)

Sends `string` with the given `encoding` and terminates the stream with EOF
or FIN. This is useful to reduce the number of packets sent.

주어진 `encoding`으로 `string`을 보내고 EOF나 FIN로 스트림을 종료시킨다. 이것은 보내는 패킷의 양을 줄이는데 유용하다.

### stream.end(buffer)

Same as above but with a `buffer`.

앞에와 동일하지만 `buffer`를 사용한다.

### stream.destroy()

Closes the underlying file descriptor. Stream will not emit any more events.

기반이 되는 파일 디스크립터를 닫는다. 스트림은 더이상 이벤트를 발생시키지 않을 것이다.

### stream.destroySoon()

After the write queue is drained, close the file descriptor. `destroySoon()`
can still destroy straight away, as long as there is no data left in the queue
for writes.

쓰기 큐(write queue)를 모두 소모한 후에 파일 디스크립터를 닫는다. `destroySoon()`는 쓰기를 위한 큐에 데이터가 남아있지 않게 되자마자 바로 제거할 수 있다.
