## UDP / Datagram Sockets

Datagram sockets are available through `require('dgram')`.  Datagrams are most commonly
handled as IP/UDP messages but they can also be used over Unix domain sockets.
데이터그램 소캣들은 `require('dgram')`에서 사용할 수 있다. 데이터그램은 대부분의 경우 IP/UDP
메시지 처리되지만 UNIX 도메인 소켓을 사용할 수 있다.

### Event: 'message'

`function (msg, rinfo) { }`

Emitted when a new datagram is available on a socket.  `msg` is a `Buffer` and `rinfo` is
an object with the sender's address information and the number of bytes in the datagram.
소켓에 새 데이터 그램이 도착했을 때 생성된다. `msg` 는 `Buffer`이고, `rinfo`는 발신자 주소정보와
데이터그램의 사이즈(bytes)를 가진 객체이다.

### Event: 'listening'

`function () { }`

Emitted when a socket starts listening for datagrams.  This happens as soon as UDP sockets
are created.  Unix domain sockets do not start listening until calling `bind()` on them.
소켓이 데이터그램 수신을 시작할때 생성된다. 이것은 UDP 소켓이 작성되는 즉시 발생한다. UNIX 도메인
소켓은 `bind()`를 호출할 때까지 리스닝을 시작하지 않는다.

### Event: 'close'

`function () { }`

Emitted when a socket is closed with `close()`.  No new `message` events will be emitted
on this socket.
`close()`이 소켓을 받을 때 생성된다. 이 소켓은 새로운 `message` 이벤트가 생성되지 않는다.

### dgram.createSocket(type, [callback])

Creates a datagram socket of the specified types.  Valid types are:
`udp4`, `udp6`, and `unix_dgram`.
지정된 형식의 데이터그램을 만든다. 유효한 타입은 `udp4`, `udp6`, `unix_dgram` 이다.

Takes an optional callback which is added as a listener for `message` events.
`message` 이벤트 리스너가 더해진 콜백으로 이동한다. 필수는 아니다.

### dgram.send(buf, offset, length, path, [callback])

For Unix domain datagram sockets, the destination address is a pathname in the filesystem.
An optional callback may be supplied that is invoked after the `sendto` call is completed
by the OS.  It is not safe to re-use `buf` until the callback is invoked.  Note that
unless the socket is bound to a pathname with `bind()` there is no way to receive messages
on this socket.
UNIX 도메인의 데이터그램 소켓 용. 수신지 종단 주소는 파일 시스템의 경로명이다. 옵션인 콜백은 OS에서
`sendto` 호출이 완료된 후에 시작되기 위하여 제공된다. 콜백이 호출될 때까지 `buf` 재사용은 안전하지 않다.
`bind()`로 소켓이 경로명에 바인딩되지 않는 한, 이 소켓으로 메시지를 받을 수 없다는 것에 주의.

Example of sending a message to syslogd on OSX via Unix domain socket `/var/run/syslog`:
Unix 도메인소켓 `/var/run/syslog`로 OSX에서 syslogd에 메세지를 보내는 예제:

    var dgram = require('dgram');
    var message = new Buffer("A message to log.");
    var client = dgram.createSocket("unix_dgram");
    client.send(message, 0, message.length, "/var/run/syslog",
      function (err, bytes) {
        if (err) {
          throw err;
        }
        console.log("Wrote " + bytes + " bytes to socket.");
    });

### dgram.send(buf, offset, length, port, address, [callback])

For UDP sockets, the destination port and IP address must be specified.  A string
may be supplied for the `address` parameter, and it will be resolved with DNS.  An
optional callback may be specified to detect any DNS errors and when `buf` may be
re-used.  Note that DNS lookups will delay the time that a send takes place, at
least until the next tick.  The only way to know for sure that a send has taken place
is to use the callback.
UDP 소켓 용. 수신지 종단의 포트와 IP주소는 필수다. `address`인자에 문자열을 넘기면, DNS와
해결된다. DNS 오류 발생시 와 `buf`가 재사용 가능하게 된 경우를 위해 콜백을 지정할 수 있다.
콜백은 옵션이다. DNS 조회는 발신시간을 (적어도 다음 Tick 까지) 지연시킬것이란 걸 참고하라.
발신을 했다는 것을 확실히 아는 유일한 방법은 콜백을 사용하는 것이다.

Example of sending a UDP packet to a random port on `localhost`;
`localhost` 에서 임의의 포트로 UDP 패킷을 발신하는 예제.

    var dgram = require('dgram');
    var message = new Buffer("Some bytes");
    var client = dgram.createSocket("udp4");
    client.send(message, 0, message.length, 41234, "localhost");
    client.close();


### dgram.bind(path)

For Unix domain datagram sockets, start listening for incoming datagrams on a
socket specified by `path`. Note that clients may `send()` without `bind()`,
but no datagrams will be received without a `bind()`.
UNIX 도메인 데이타그램 소켓용. `path`로 명시된 소켓에서 데이터그램의 수신대기를 시작한다.
클라이언트는 `bind()` 없이 `send()` 할수 있지만, `bind()` 하지 않고 데이터그램을 수신하는 것은 아니다.

Example of a Unix domain datagram server that echoes back all messages it receives:
수신한 모든 메세지를 출력하는 Unix 도메인의 데이타그램 예제.

    var dgram = require("dgram");
    var serverPath = "/tmp/dgram_server_sock";
    var server = dgram.createSocket("unix_dgram");

    server.on("message", function (msg, rinfo) {
      console.log("got: " + msg + " from " + rinfo.address);
      server.send(msg, 0, msg.length, rinfo.address);
    });

    server.on("listening", function () {
      console.log("server listening " + server.address().address);
    })

    server.bind(serverPath);

Example of a Unix domain datagram client that talks to this server:
이 서버와 통신하는 UNIX 도메인 데이타그램 클라이언트 예제.

    var dgram = require("dgram");
    var serverPath = "/tmp/dgram_server_sock";
    var clientPath = "/tmp/dgram_client_sock";

    var message = new Buffer("A message at " + (new Date()));

    var client = dgram.createSocket("unix_dgram");

    client.on("message", function (msg, rinfo) {
      console.log("got: " + msg + " from " + rinfo.address);
    });

    client.on("listening", function () {
      console.log("client listening " + client.address().address);
      client.send(message, 0, message.length, serverPath);
    });

    client.bind(clientPath);

### dgram.bind(port, [address])

For UDP sockets, listen for datagrams on a named `port` and optional `address`.  If
`address` is not specified, the OS will try to listen on all addresses.
UDP 소켓용. 명명된 `port`과 옵셩인 `address` 데이터 그램을 수신대기 한다. `address`가 지정되지
않으면 OS 는 모두 `address`에서 수신을 시도할 것이다.

Example of a UDP server listening on port 41234:
41234 포트에서 수신 대기하는 UDP 서버 예제.

    var dgram = require("dgram");

    var server = dgram.createSocket("udp4");

    server.on("message", function (msg, rinfo) {
      console.log("server got: " + msg + " from " +
        rinfo.address + ":" + rinfo.port);
    });

    server.on("listening", function () {
      var address = server.address();
      console.log("server listening " +
          address.address + ":" + address.port);
    });

    server.bind(41234);
    // server listening 0.0.0.0:41234


### dgram.close()

Close the underlying socket and stop listening for data on it.  UDP sockets
automatically listen for messages, even if they did not call `bind()`.
하층 소켓을 닫고 여기서 데이터의 수신 대기를 중지한다. 비록 `bind()` 가 호출되지 않더라도,
UDP 소켓은 자동으로 메세지를 기다린다.

### dgram.address()

Returns an object containing the address information for a socket.  For UDP sockets,
this object will contain `address` and `port`.  For Unix domain sockets, it will contain
only `address`.
소켓의 주소 정보를 가지고 있는 객체를 반환한다. UDP 소켓에서 이 객체는 `address`와 `port`를 가지고 있다.
UNIX 도메인 소켓에서는 `address`만 가지고 있다.

### dgram.setBroadcast(flag)

Sets or clears the `SO_BROADCAST` socket option.  When this option is set, UDP packets
may be sent to a local interface's broadcast address.
소켓 옵션 `SO_BROADCAST` 을 설정하거나 해제한다. 이 옵션을 세팅하면, UDP 패킷은 로컬
인터페이스의 브로드 캐스트를 위한 주소로 전송된다.

### dgram.setTTL(ttl)

Sets the `IP_TTL` socket option.  TTL stands for "Time to Live," but in this context it
specifies the number of IP hops that a packet is allowed to go through.  Each router or
gateway that forwards a packet decrements the TTL.  If the TTL is decremented to 0 by a
router, it will not be forwarded.  Changing TTL values is typically done for network
probes or when multicasting.
소켓 옵션의 `IP_TTL`을 설정한다. TTL은 "수명"을 나타내지만, 이 컨텍스트에서 패킷이 통과할 수 있는
IP 홉 수를 지정한다. 각 라우터 또는 게이트웨이 패킷을 전달하기 위해 TTL을 감소시킨다. 라우터에서
TTL 이 0으로 감소하면, 그것은 전달되지 않는다. TTL 값 변경은 일반적으로 네트워크 조사와 멀티
캐스트로 사용된다.

The argument to `setTTL()` is a number of hops between 1 and 255.  The default on most
systems is 64.
`setTTL()` 의 인자는 1~255의 홉 수이다. 기본값은 대부분 시스템이 64이다.

### dgram.setMulticastTTL(ttl)

Sets the `IP_MULTICAST_TTL` socket option.  TTL stands for "Time to Live," but in this
context it specifies the number of IP hops that a packet is allowed to go through,
specifically for multicast traffic.  Each router or gateway that forwards a packet
decrements the TTL. If the TTL is decremented to 0 by a router, it will not be forwarded.
소켓 옵션의 `IP_MULTICAST_TTL` 을 설정한다. TTL은 "수명"을 나타낸다. 이 컨텍스트에서 멀티 캐스트
트래픽에서 패킷이 통과할 수 있는 IP 홉 수를 지정한다. 각각의 라우터 또는 게이트웨이는
패킷을 전송할 때 TTL을 감소시킨다. TTL이 라우터에서 0까지 감소되면 패킷은 전송되지 않는다.

The argument to `setMulticastTTL()` is a number of hops between 0 and 255.  The default on most
systems is 64.
`setMulticastTTL()` 의 인수는 홉을 나타내는 수치로, 0에서 255 사이이다. 대부분의 시스템에서 기본값은 64이다.

### dgram.setMulticastLoopback(flag)

Sets or clears the `IP_MULTICAST_LOOP` socket option.  When this option is set, multicast
packets will also be received on the local interface.
`IP_MULTICAST_LOOP` 소켓 옵션을 설정하거나 지운다. 이 옵션이 설정되면 멀티 캐스트 패킷은 로컬 인터페이스에서
수신할 수 있도록 한다.

### dgram.addMembership(multicastAddress, [multicastInterface])

Tells the kernel to join a multicast group with `IP_ADD_MEMBERSHIP` socket option.
`IP_ADD_MEMBERSHIP` 소켓 옵션을 설정하여 멀티 캐스트 그룹에 참여하는 것을 커널에 전달한다.

If `multicastInterface` is not specified, the OS will try to add membership to all valid
interfaces.
`multicastInterface` 가 지정되지 않은 경우 OS는 모든 유효한 인터페이스를 구성원으로 추가한다.

### dgram.dropMembership(multicastAddress, [multicastInterface])

Opposite of `addMembership` - tells the kernel to leave a multicast group with
`IP_DROP_MEMBERSHIP` socket option. This is automatically called by the kernel
when the socket is closed or process terminates, so most apps will never need to call
this.
`addMembership`의 반대이다 - `IP_DROP_MEMBERSHIP` 소켓 옵션은 멀티 캐스트 그룹에서 빠지는 것을
커널에 전달한다.  이것은 소켓의 종료 시 및 프로세스 종료시 커널에 의해 자동으로 호출되므로
대부분의 애플리케이션은 이것을 호출할 필요가 없다.

If `multicastInterface` is not specified, the OS will try to drop membership to all valid
interfaces.
`multicastInterface` 가 지정되지 않은 경우 OS는 모든 유효한 인터페이스를 구성원에서 삭제하려고 한다.
