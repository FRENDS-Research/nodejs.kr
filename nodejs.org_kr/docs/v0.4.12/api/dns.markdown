## DNS

Use `require('dns')` to access this module.
이 모듈에 엑세스하려면 `require('dns')`를 사용한다.

Here is an example which resolves `'www.google.com'` then reverse
resolves the IP addresses which are returned.
여기, `'www.google.com'`을 풀이(resolve)하여, 반환되는 IP주소를 역방향
풀이(reverse resolves)하는 하는 예가 있다.

    var dns = require('dns');

    dns.resolve4('www.google.com', function (err, addresses) {
      if (err) throw err;

      console.log('addresses: ' + JSON.stringify(addresses));

      addresses.forEach(function (a) {
        dns.reverse(a, function (err, domains) {
          if (err) {
            console.log('reverse for ' + a + ' failed: ' +
              err.message);
          } else {
            console.log('reverse for ' + a + ': ' +
              JSON.stringify(domains));
          }
        });
      });
    });

### dns.lookup(domain, family=null, callback)

Resolves a domain (e.g. `'google.com'`) into the first found A (IPv4) or
AAAA (IPv6) record.
도메인 (예 `'google.com'`)를 풀이(resolve)하여 처음 발견된 A (IPv4) 또는
AAAA (IPv6) 기록한다.

The callback has arguments `(err, address, family)`.  The `address` argument
is a string representation of a IP v4 or v6 address. The `family` argument
is either the integer 4 or 6 and denotes the family of `address` (not
necessarily the value initially passed to `lookup`).
콜백은 인수 `(err, address, family)` 를 가지고 있다. `address` 인수는 IP v4 또는
v6 주소 표기 문자열이다. `family` 인수는 4,6 정수 중 하나이고 `address` 와 쌍이다.
(이 값은 반드시 먼저 `lookup` 에 전달할 필요는 없다. )


### dns.resolve(domain, rrtype='A', callback)

Resolves a domain (e.g. `'google.com'`) into an array of the record types
specified by rrtype. Valid rrtypes are `A` (IPV4 addresses), `AAAA` (IPV6
addresses), `MX` (mail exchange records), `TXT` (text records), `SRV` (SRV
records), `PTR` (used for reverse IP lookups), `NS` (name server records)
and `CNAME` (canonical name records).
rrtype이 명시된 레코드 타입 배열의 도메인(예 `'google.com'`) 풀이. 유효한 rrtype은
`A` (IPV4 주소) `AAAA` (IPV6 주소) `MX` (mail exchange 레코드), `TXT`
(텍스트 레코드), `SRV` (SRV 레코드), `PTR` (IP를 역방향으로 조회하는 데 사용된다) 이다.

The callback has arguments `(err, addresses)`.  The type of each item
in `addresses` is determined by the record type, and described in the
documentation for the corresponding lookup methods below.
콜백은 인수 `(err, addresses)` 를 가진다. `addresses` 각 항목의 타입은 레코드 타입에
의해 결정되고, 해당조회 방법은 아래 문서에 설명되어있다.

On error, `err` would be an instanceof `Error` object, where `err.errno` is
one of the error codes listed below and `err.message` is a string describing
the error in English.
오류 발생시, `err` 는 `Error` 개체의 인스턴스이며, `err.errno` 은 아래 오류 코드 중
하나이고 `err.message` 는 영문 에러 문자열이다.


### dns.resolve4(domain, callback)

The same as `dns.resolve()`, but only for IPv4 queries (`A` records).
`addresses` is an array of IPv4 addresses (e.g.
`['74.125.79.104', '74.125.79.105', '74.125.79.106']`).
`dns.resolve()` 과 같다. 단, IPv4 질의(`A` 레코드들)만 지원한다. `addresses` 는
IPv4 주소 배열이다. (예. [`'74.125.79.104'`, `'74.125.79.105'`, `'74.125.79.106'`] ).

### dns.resolve6(domain, callback)

The same as `dns.resolve4()` except for IPv6 queries (an `AAAA` query).
IPv6 질의(`AAAA` 질의) 인것 제외하고는 `dns.resolve4()` 와 동일하다.


### dns.resolveMx(domain, callback)

The same as `dns.resolve()`, but only for mail exchange queries (`MX` records).
`dns.resolve()` 과 같다. 단, mail exchange 질의(`MX` 레코드들)만 지원한다.

`addresses` is an array of MX records, each with a priority and an exchange
attribute (e.g. `[{'priority': 10, 'exchange': 'mx.example.com'},...]`).
`addresses` 는 MX 레코드 배열로, 각각은 `priority`와 e`xchange` 속성을 갇는다.
(예. `[{'priority': 10, 'exchange': 'mx.example.com'},...]`).

### dns.resolveTxt(domain, callback)

The same as `dns.resolve()`, but only for text queries (`TXT` records).
`addresses` is an array of the text records available for `domain` (e.g.,
`['v=spf1 ip4:0.0.0.0 ~all']`).
`dns.resolve()`와 같다. 단, 텍스트 질의(`TXT` 레코드들)만 지원한다. `addresses`는
`domain`에서 가능한 텍스트 레코드 배열이다. (예,  `['v=spf1 ip4:0.0.0.0 ~all']`).

### dns.resolveSrv(domain, callback)

The same as `dns.resolve()`, but only for service records (`SRV` records).
`addresses` is an array of the SRV records available for `domain`. Properties
of SRV records are priority, weight, port, and name (e.g.,
`[{'priority': 10, {'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]`).
`dns.resolve()`와 같다. 단, 서비스 레코드(`SRV` 레코드들)만 지원한다. `addresses` 는
`domain`에서 가능한 SRV 레코드들 배열이다. SRV 레코드의 속성은 weight, port, name이다.
(예, `[{'priority': 10, {'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]`).

### dns.reverse(ip, callback)

Reverse resolves an ip address to an array of domain names.
IP주소로 도메인명 배열을 역방향 풀이(resolves)한다.

The callback has arguments `(err, domains)`.
콜백은 `(err, domains)` 인수를 가진다.

### dns.resolveNs(domain, callback)

The same as `dns.resolve()`, but only for name server records (`NS` records).
`addresses` is an array of the name server records available for `domain`
(e.g., `['ns1.example.com', 'ns2.example.com']`).
`dns.resolve()`와 동일하지만, 네임 서버 레코드(`NS` 레코드)는 그렇지 않다. `addresses`는
`domain` 에서 사용 가능한 네임서버 레코드 배열이다. (예 `['ns1.example.com', 'ns2.example.com']`)

### dns.resolveCname(domain, callback)

The same as `dns.resolve()`, but only for canonical name records (`CNAME`
records). `addresses` is an array of the canonical name records available for
`domain` (e.g., `['bar.example.com']`).
`dns.resolve()` 와 같지만 별칭 레코드 ( `CNAME` 레코드)는 그렇지 않다. `address` 는 `domain` 에서
사용 가능한 별칭 레코드의 배열입니다 domain (eg, `[ 'bar.example.com']` ).

If there an an error, `err` will be non-null and an instanceof the Error
object.
에러가 발생하면 `err`은 null이 아닌 Error 객체의 인스턴스이다.

Each DNS query can return an error code.
각 DNS 질의는 에러코드를 반환할 수 있다.

- `dns.TEMPFAIL`: timeout, SERVFAIL or similar.
- `dns.TEMPFAIL`: 타임아웃, SERVFAIL 또는 이와 유사한것.
- `dns.PROTOCOL`: got garbled reply.
- `dns.PROTOCOL`: 알수 없는 응답.
- `dns.NXDOMAIN`: domain does not exists.
- `dns.NXDOMAIN`: 도메인이 존재하지 않음
- `dns.NODATA`: domain exists but no data of reqd type.
- `dns.NODATA`: 도메인이 존재하지만, 데이터 없음
- `dns.NOMEM`: 과정에서 메모리가 부족.
- `dns.BADQUERY`: the query is malformed.
- `dns.BADQUERY`: 질의의 형식 오류.