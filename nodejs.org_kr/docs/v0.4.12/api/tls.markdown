## TLS (SSL)

Use `require('tls')` to access this module.

이 모듈에 접근하려면 `require('tls')`를 사용해라.

The `tls` module uses OpenSSL to provide Transport Layer Security and/or
Secure Socket Layer: encrypted stream communication.

`tls`모듈은 전송계층보안(Transport Layer Security)와 암호화된 스트림통신인
보안소켓계층(Secure Socket Layer)를 지원하기 위해서 OpenSSL을 사용한다.

TLS/SSL is a public/private key infrastructure. Each client and each
server must have a private key. A private key is created like this

TLS/SSL은 공개키/개인키 구조이다. 각 클라이언트와 서버는 개인키를 가지고 
있어야 한다. 개인키는 다음처럼 생성한다.

    openssl genrsa -out ryans-key.pem 1024

All severs and some clients need to have a certificate. Certificates are public
keys signed by a Certificate Authority or self-signed. The first step to
getting a certificate is to create a "Certificate Signing Request" (CSR)
file. This is done with:

모든 서버와 몇몇 클라이언트는 인증서를 가져야 한다. 인증서는 CA(Certificate Authority)로
서명되거나 자체서명된 공개키이다. 인증서를 얻기 위한 첫 단계는 CSR(Certificate Signing Request)파일을
생성하는 것이다. 이것은 다음 명령어로 수행한다.

    openssl req -new -key ryans-key.pem -out ryans-csr.pem

To create a self-signed certificate with the CSR, do this:

CSR로 자제서명된 인증서를 생성하려면 아래 명령어를 실행해라:

    openssl x509 -req -in ryans-csr.pem -signkey ryans-key.pem -out ryans-cert.pem

Alternatively you can send the CSR to a Certificate Authority for signing.

아니면 서명을 위해서 CA에 CSR을 보낼 수 있다. 

(TODO: docs on creating a CA, for now interested users should just look at
`test/fixtures/keys/Makefile` in the Node source code)

(TODO: CA생성에 대한 문서, 관심있는 유저는 노드 소스코드의
`test/fixtures/keys/Makefile`파일을 봐라)


### s = tls.connect(port, [host], [options], callback)

Creates a new client connection to the given `port` and `host`. (If `host`
defaults to `localhost`.) `options` should be an object which specifies

주어진 `port`와 `host`로 새로운 클라이언트 커넥션을 생성한다. (`host`의
기본값은 `localhost`이다.) `options`는 명시된 객체여야 한다.

  - `key`: A string or `Buffer` containing the private key of the server in
    PEM format. (Required)

  - `key`: 문자열이나 PEM형식으로 서버의 개인키를 가지고 있는 `Buffer`(필수)

  - `cert`: A string or `Buffer` containing the certificate key of the server in
    PEM format.
  - `cert`: 문자열이나 PEM형식으로 서버의 인증서 키를 담고 있는 `Buffer`.

  - `ca`: An array of strings or `Buffer`s of trusted certificates. If this is
    omitted several well known "root" CAs will be used, like VeriSign.
    These are used to authorize connections.

  - `ca`: 문자열의 배열이거나 신뢰된 인증서의 `Buffer`. 생략된다면 VeriSign같은 
    유명한 "루트" CA를 사용한다. 커넥션을 인가하기 위해 사용한다.

  - `NPNProtocols`: An array of string or `Buffer` containing supported NPN
    protocols. `Buffer` should have following format: `0x05hello0x05world`, where
    first byte is next protocol name's length. (Passing array should usually be
    much simplier: `['hello', 'world']`.)

  - `NPNProtocols`: 문자열의 배열이거나 지원하는 NPN 프로토콜을 담고 있는 `Buffer`.
    `Buffer`는 다음과 같은 형식이어야 한다: `0x05hello0x05world`, 첫 바이트는
    다음 프로토콜 이름의 길이이다. (배열을 전달하는 것이 보통 더 간단하다: `['hello', 'world']`)

  - `servername`: Servername for SNI (Server Name Indication) TLS extension.

  - `servername`: SNI(Server Name Indication) TLS 확장을 위한 서버명.

`tls.connect()` returns a [CleartextStream](#tls.CleartextStream) object.

`tls.connect()`는 [CleartextStream](#tls.CleartextStream)객체를 리턴한다. 

After the TLS/SSL handshake the `callback` is called. The `callback` will be
called no matter if the server's certificate was authorized or not. It is up
to the user to test `s.authorized` to see if the server certificate was signed
by one of the specified CAs. If `s.authorized === false` then the error
can be found in `s.authorizationError`. Also if NPN was used - you can check
`s.npnProtocol` for negotiated protocol.

TLS/SSL 핸드쉐이커이후에 `callback`를 호출한다. 서버의 인증서가 인가여부에 상관없이
`callback`을 호출한다. 이는 서버 인증서가 명시된 CA중의 하나로 서명되었는지 확인하기 위해
`s.authorized`를 확인하는 유저에게 달려있다. `s.authorized === false`이면 에러를
`s.authorizationError`에서 찾을 수 있다. 또한 NPN을 사용했다면 협상된 프로토콜을 위해서
`s.npnProtocol`를 체크할 수 있다.


### STARTTLS

In the v0.4 branch no function exists for starting a TLS session on an
already existing TCP connection.  This is possible it just requires a bit of
work. The technique is to use `tls.createSecurePair()` which returns two
streams: an encrypted stream and a cleartext stream. The encrypted stream is
then piped to the socket, the cleartext stream is what the user interacts with
thereafter.

v0.4 브랜치에서는 이미 존재하는 TCP커넥션에 TLS 세션을 시작하는 함수가 존재하지 않았다. 
이것은 약간의 작업으로 가능한 것이다. 기술은 두 스트림(암호화된 스트림과 평문스크림)을 
돌려주는 `tls.createSecurePair()`를 사용하는 것이다. 암호화된 스크림은 소켓에 연결되고
평문 스크림은 이후의 사용자가 상호작용한 것이다.

[이것을 수행하는 코드.](http://gist.github.com/848444)

### pair = tls.createSecurePair([credentials], [isServer], [requestCert], [rejectUnauthorized])

Creates a new secure pair object with two streams, one of which reads/writes
encrypted data, and one reads/writes cleartext data.
Generally the encrypted one is piped to/from an incoming encrypted data stream,
and the cleartext one is used as a replacement for the initial encrypted stream.

두 스트림(읽기/쓰기 암호화된 데이터와 읽기/쓰기 평문 데이터)으로 
새로운 보안 pair객체를 생성한다. 
보통 암호화된 데이터는 들어오는 암호화된 데이터 스트림으로 이동되고
평문데이터는 암호화스트림을 초기화하기 위한 치환자로 사용된다.

 - `credentials`: A credentials object from crypto.createCredentials( ... )

 - `credentials`: crypto.createCredentials( ... )의 인증정보 객체

 - `isServer`: A boolean indicating whether this tls connection should be
   opened as a server or a client.

 - `isServer`: 이 tls 커넥션이 서버로 열렸는지 클라이언트로 열렸는지를 나타내는
   boolean값

 - `requestCert`: A boolean indicating whether a server should request a
   certificate from a connecting client. Only applies to server connections.

 - `requestCert`: 서버가 연결된 클라이언트한테 인증서를 요청해야 하는지를 나타내는
   boolean 값. 서버커넥션에만 적용된다.

 - `rejectUnauthorized`: A boolean indicating whether a server should
   automatically reject clients with invalid certificates. Only applies to
   servers with `requestCert` enabled.

 - `rejectUnauthorized`: 서버가 유효하지 않은 인증서를 가진 클라이언트를 자동적으로
   거부해야 하는지를 나타내는 boolean값. `requestCert`가 허용된 서버에만 적용된다.

`tls.createSecurePair()` returns a SecurePair object with
[cleartext](#tls.CleartextStream) and `encrypted` stream properties.

`tls.createSecurePair()`는 [cleartext](#tls.CleartextStream)와 `encrypted`스크립 속성을 
가진 SecurePair객체를 리턴한다.

#### Event: 'secure'

The event is emitted from the SecurePair once the pair has successfully
established a secure connection.

pair가 성공적으로 보안커넥션을 만들면 SecurePair에서 발생하는 이벤트이다.

Similarly to the checking for the server 'secureConnection' event,
pair.cleartext.authorized should be checked to confirm whether the certificate
used properly authorized.

서버의 'secureConnection'이벤트를 확인하는 것과 비슷하게 pair.cleartext.authorized는 
사용된 인증서가 적절하게 인가되었는지를 확인한다.

### tls.Server

This class is a subclass of `net.Server` and has the same methods on it.
Instead of accepting just raw TCP connections, this accepts encrypted
connections using TLS or SSL.

이 클래스는 `net.Server`의 서브클래스이고 같은 메서드를 갖는다.
그냥 raw TCP 커넥션을 받아들이는 대신에 TLS나 SSL을 사용하는 암호화 커넥션을 받아들인다.

Here is a simple example echo server:

간단한 예제용 에코서버가 있다:

    var tls = require('tls');
    var fs = require('fs');

    var options = {
      key: fs.readFileSync('server-key.pem'),
      cert: fs.readFileSync('server-cert.pem')
    };

    tls.createServer(options, function (s) {
      s.write("welcome!\n");
      s.pipe(s);
    }).listen(8000);


You can test this server by connecting to it with `openssl s_client`:

`openssl s_client`로 접속해 봄으로써 이 서버를 테스트할 수 있다:


    openssl s_client -connect 127.0.0.1:8000


#### tls.createServer(options, secureConnectionListener)

This is a constructor for the `tls.Server` class. The options object
has these possibilities:

`tls.Server`클래스의 생성자이다. options객체는 다음과 같은 것들이 가능하다.

  - `key`: A string or `Buffer` containing the private key of the server in
    PEM format. (Required)
   
  - `key`: 문자열이나 PEM형식의 서버 개인키를 담고 있는 `Buffer`(필수)

  - `cert`: A string or `Buffer` containing the certificate key of the server in
    PEM format. (Required)

  - `cert`: 문자열이나 PEM형식의 서버의 증명서키를 담고 있는 `Buffer`(필수)

  - `ca`: An array of strings or `Buffer`s of trusted certificates. If this is
    omitted several well known "root" CAs will be used, like VeriSign.
    These are used to authorize connections.

  - `ca`: 문자열의 배열이거나 신뢰된 인증서의 `Buffer`. 생략된다면 VeriSign같은 
    유명한 "루트" CA를 사용한다. 커넥션을 인가하기 위해 사용한다.

  - `requestCert`: If `true` the server will request a certificate from
    clients that connect and attempt to verify that certificate. Default:
    `false`.

  - `requestCert`: `true`이면 서버는 연결한 클리이언트한테 인증서를 요청하고
    인증서를 증명하려고 시도할 것이다. 기본값: `false`

  - `rejectUnauthorized`: If `true` the server will reject any connection
    which is not authorized with the list of supplied CAs. This option only
    has an effect if `requestCert` is `true`. Default: `false`.

  - `rejectUnauthorized`: `true`이면 서버는 제공된 CA 리스트로 인가되지 않은 
    모든 커넥션을 거부할 것이다. 이 옵션은 오직 `requestCert`가 `true`일때만 
    효과가 있다. 기본값: `false`

  - `NPNProtocols`: An array or `Buffer` of possible NPN protocols. (Protocols
    should be ordered by their priority).

  - `NPNProtocols`: 배열이거나 가능한 NPN 프로토콜의 `Buffer`. (프로토콜은
    중요도에 따라 정렬되어야 한다.)

  - `SNICallback`: A function that will be called if client supports SNI TLS
    extension. Only one argument will be passed to it: `servername`. And
    `SNICallback` should return SecureContext instance.
    (You can use `crypto.createCredentials(...).context` to get proper
    SecureContext). If `SNICallback` wasn't provided - default callback with
    high-level API will be used (see below).

  - `SNICallback`: 클라이언트가 SNI TLS확장을 지원할때 호출되는 함수이다. 단 하나의
    아규먼트만 전달된다: `servername`. 그리고 `SNICallback`은 SecureContext 인스턴스를
    리턴해야 한다.
    (적당한 SecureContext를 억기위해서 `crypto.createCredentials(...).context`를 사용할 수
     있다.) `SNICallback`이 제공되지 않았다면 고레벨 API를 가진 기본 콜백이 사용된다.(아래 참조)

#### Event: 'secureConnection'

`function (cleartextStream) {}`

This event is emitted after a new connection has been successfully
handshaked. The argument is a instance of
[CleartextStream](#tls.CleartextStream). It has all the common stream methods
and events.

새로운 커넥션이 성공적으로 핸드쉐이크되었을때 발생하는 이벤트이다. 아규먼트는
[CleartextStream](#tls.CleartextStream)의 인스턴스이다. 공통적인 모든 스트림
메서드와 이벤트를 가지고 있다.

`cleartextStream.authorized` is a boolean value which indicates if the
client has verified by one of the supplied certificate authorities for the
server. If `cleartextStream.authorized` is false, then
`cleartextStream.authorizationError` is set to describe how authorization
failed. Implied but worth mentioning: depending on the settings of the TLS
server, you unauthorized connections may be accepted.
`cleartextStream.npnProtocol` is a string containing selected NPN protocol.
`cleartextStream.servername` is a string containing servername requested with
SNI.

`cleartextStream.authorized`는 클라이언트가 서버에 대한 제공된 CA중의 하나로
증명되었는지를 나타내는 boolean값이다. `cleartextStream.authorized`가 false이면
`cleartextStream.authorizationError`는 어떻게 인가가 실패했는지를 나타내기 위해서
설정된다. 함축적이지만 의미있다: TLS서버의 설정에 따라 인가되지 않은 커넥션도
받아들여질 수 있다.
`cleartextStream.npnProtocol`는 선택한 NPN 프로토콜을 담고 있는 문자열이다.
`cleartextStream.servername`는 SNI로 요청된 서버명을 담고 있는 문자열이다.


#### server.listen(port, [host], [callback])

Begin accepting connections on the specified `port` and `host`.  If the
`host` is omitted, the server will accept connections directed to any
IPv4 address (`INADDR_ANY`).

명시된 `port`와 `host`로 커넥션 허용을 시작한다. `host`가 생략되면 서버는
모든 IPv4주소(`INADDR_ANY`)로 연결되 모든 커넥션을 받아들일 것이다.

This function is asynchronous. The last parameter `callback` will be called
when the server has been bound.

이 함수는 비동기이다. 마지막 파라미터인 `callback`은 서버가 바인드되었을 때
실행된다.

See `net.Server` for more information.

더 자세한 내용은 `net.Server`를 봐라.


#### server.close()

Stops the server from accepting new connections. This function is
asynchronous, the server is finally closed when the server emits a `'close'`
event.

서버가 새로운 커넥션을 받아들이는 것을 멈춘다. 이 함수는 비동기이고 서버는
`'close'`이벤트가 발생했을때 결국 종료된다.

#### server.addContext(hostname, credentials)

Add secure context that will be used if client request's SNI hostname is
matching passed `hostname` (wildcards can be used). `credentials` can contain
`key`, `cert` and `ca`.

클라이언트 요청의 SNI 호스트명이 전달된 `hostname`(와일드카드를 사용할 수 있다.)와
일치할때 사용되는 보안컨텍스트를 추가한다. `credentials`는 `key`, `cert`, `ca`를 
포함할 수 있다.

#### server.maxConnections

Set this property to reject connections when the server's connection count
gets high.

서버의 커넥션수가 많아졌을때 커넥션을 거부하기 위해서 설정한다.

#### server.connections

The number of concurrent connections on the server.

서버의 동시커넥션의 수.


### tls.CleartextStream

This is a stream on top of the *Encrypted* stream that makes it possible to
read/write an encrypted data as a cleartext data.

평문데이터로 암호화된 데이터를 읽기/쓰기가 가능하도록 *암호화된*스트림상의 스트림이다.

This instance implements a duplex [Stream](streams.html#streams) interfaces.
It has all the common stream methods and events.

듀플렉스(duplex) [스트림](streams.html#streams) 인터페이스를 구현한 인스턴스이다.

#### cleartextStream.authorized

A boolean that is `true` if the peer certificate was signed by one of the
specified CAs, otherwise `false`

peer 인즌서가 명시된 CA중의 하나로 서명되면 `true`이고 그렇지 않으면 `false`이다.

#### cleartextStream.authorizationError

The reason why the peer's certificate has not been verified. This property
becomes available only when `cleartextStream.authorized === false`.

왜 peer의 인증서가 증명되지 않았는지에 대한 사유이다. 
`cleartextStream.authorized === false`일때만 이용가능한 속성이다.

#### cleartextStream.getPeerCertificate()

Returns an object representing the peer's certicicate. The returned object has
some properties corresponding to the field of the certificate.

peear의 인증서를 나타내는 객체를 리턴한다. 리턴된 객체는 인증서의 필드에 대응되는
속성을 갖고 있다.

Example:

예:

    { subject: 
       { C: 'UK',
         ST: 'Acknack Ltd',
         L: 'Rhys Jones',
         O: 'node.js',
         OU: 'Test TLS Certificate',
         CN: 'localhost' },
      issuer: 
       { C: 'UK',
         ST: 'Acknack Ltd',
         L: 'Rhys Jones',
         O: 'node.js',
         OU: 'Test TLS Certificate',
         CN: 'localhost' },
      valid_from: 'Nov 11 09:52:22 2009 GMT',
      valid_to: 'Nov  6 09:52:22 2029 GMT',
      fingerprint: '2A:7A:C2:DD:E5:F9:CC:53:72:35:99:7A:02:5A:71:38:52:EC:8A:DF' }

If the peer does not provide a certificate, it returns `null` or an empty
object.

peer가 인증서를 제공하지 않으면 `null`을 리턴하거나 빈 객체를 리턴한다.

