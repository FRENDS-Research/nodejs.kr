## 버퍼 (Buffers)

Pure Javascript is Unicode friendly but not nice to binary data.  When
dealing with TCP streams or the file system, it's necessary to handle octet
streams. Node has several strategies for manipulating, creating, and
consuming octet streams.

순수 자바스크립트는 유니코드는 사용하기 편하지만 바이너리 데이터에는 그다지 좋지 않다. TCP스트림이나 파일시스템을 다룰 때 옥텟 스트림(octet stream)을 다룰 필요가 있다. 노드는 옥텟 스트림을 조작하고, 생성하고 사용하기 위해서 여러가지 전략을 가지고 있다.

Raw data is stored in instances of the `Buffer` class. A `Buffer` is similar
to an array of integers but corresponds to a raw memory allocation outside
the V8 heap. A `Buffer` cannot be resized.

로우데이터는 `Buffer`클래스의 인스턴스에 저장된다. `Buffer`은 인티저의 배열과 유사하지만 V8 Heap 밖에 로우 메모리 할당에 해당한다. `Buffer`은 사이즈가 변경될 수 없다.

The `Buffer` object is global.

`Buffer` 객체는 전역객체이다.

Converting between Buffers and JavaScript string objects requires an explicit encoding
method.  Here are the different string encodings;

버터와 자바스크립트 문자열 객체간의 변환은 명시적인 인코딩 방식을 요구한다. 여기 여러가지 문자열 인코딩이 있다;

* `'ascii'` - for 7 bit ASCII data only.  This encoding method is very fast, and will
strip the high bit if set.
Note that this encoding converts a null character (`'\0'` or `'\u0000'`) into
`0x20` (character code of a space). If you want to convert a null character
into `0x00`, you should use `'utf8'`.

* `'ascii'` - 7비트 아스키 데이터만은 위한 것이다. 이 인코딩 방식은 아주 빠르고 설정된다면 높은 비트는 제거할 것이다.
이 인코딩은 null 캐릭터(`'\0'` 또는 `'\u0000'`)를 `0x20`(공백에 대한 캐릭터 코드)로 변환한다는 것을 주의해야 한다. null 캐릭터를 `0x00`로 변환하고 싶다면 `'utf8'`를 사용해야 한다.

* `'utf8'` - Multi byte encoded Unicode characters.  Many web pages and other document formats use UTF-8.

* * `'utf8'` - 멀티바이트로 인코딩된 유티코드 캐릭터이다. 많은 웹페이지들과 다른 문서포맷들은 UTF-8을 사용한다.

* `'ucs2'` - 2-bytes, little endian encoded Unicode characters. It can encode
only BMP(Basic Multilingual Plane, U+0000 - U+FFFF).

* `'ucs2'` - 리틀엔디안(little endian)으로 인코딩된 2바이트 유니코드 캐릭터이다. 이것은 오직 BMP(Basic Multilingual Plane, U+0000 - U+FFFF)만 인코드할 수 있다.

* `'base64'` - Base64 string encoding.

* `'base64'` - Base64 문자열 인코딩.

* `'binary'` - A way of encoding raw binary data into strings by using only
the first 8 bits of each character. This encoding method is deprecated and
should be avoided in favor of `Buffer` objects where possible. This encoding
will be removed in future versions of Node.

* `'binary'` - 각 캐릭터의 처음 8비트만을 사용해서 로우데이터를 문자열로 인코딩하는 방식이다. 이 인코딩 방법은 이제 사용이 권장되지 않으며(deprecated) 가능하다면 `Buffer`객체에서 사용을 피해야 한다. 이 인코딩은 노드의 차후버전에서는 제거될 것이다.


### new Buffer(size)

Allocates a new buffer of `size` octets.

`size` 옥텟의 새로운 버퍼를 할당한다.

### new Buffer(array)

Allocates a new buffer using an `array` of octets.

옥텟의 `array`를 사용해서 새로운 버퍼를 할당한다.

### new Buffer(str, encoding='utf8')

Allocates a new buffer containing the given `str`.

주어진 `str`을 담고있는 새로운 버퍼를 할당한다.

### buffer.write(string, offset=0, encoding='utf8')

Writes `string` to the buffer at `offset` using the given encoding. Returns
number of octets written.  If `buffer` did not contain enough space to fit
the entire string, it will write a partial amount of the string.
The method will not write partial characters.

주어진 인코딩을 사용해서 `offset`의 버퍼에 `string`를 저장한다. 쓰여진 옥텟의 수가 리턴된다. `buffer`가 전체 문자열을 저장하기에 충분한 공간을 가지고 있지 않다면 문자열의 일부분만 저장할 것이다. 이 방식은 부분적인 캐릭터를 작성하지 않을 것이다.

Example: write a utf8 string into a buffer, then print it

예제: utf-8문자열을 버퍼로 작성하고 출력한다.

    buf = new Buffer(256);
    len = buf.write('\u00bd + \u00bc = \u00be', 0);
    console.log(len + " bytes: " + buf.toString('utf8', 0, len));

The number of characters written (which may be different than the number of
bytes written) is set in `Buffer._charsWritten` and will be overwritten the
next time `buf.write()` is called.

쓰여진 캐릭터의 수(쓰여진 바이트수와는 다를 것이다)는 `Buffer._charsWritten`에 설정되고 다음번에 `buf.write()`가 호출되었을 때 덮어써질 것이다.


### buffer.toString(encoding, start=0, end=buffer.length)

Decodes and returns a string from buffer data encoded with `encoding`
beginning at `start` and ending at `end`.

디코드하고 버퍼데이터에서 `start`부터 `end`까지 `encoding`으로 인코딩된 문자열을 리턴한다.

See `buffer.write()` example, above.

앞의 `buffer.write()` 예제를 참고해라.


### buffer[index]

Get and set the octet at `index`. The values refer to individual bytes,
so the legal range is between `0x00` and `0xFF` hex or `0` and `255`.

`index`위치의 옥텟을 가져오거나 설정한다. 값이 개별적인 바이트를 참조하기 때문에 유효한 범위는 `0x00`부터 `0xFF` hex 이거나 `0`부터 `255`이다.

Example: copy an ASCII string into a buffer, one byte at a time:

예제: ASCII문자열을 버퍼로 한번에 한바이트씩 복사한다.

    str = "node.js";
    buf = new Buffer(str.length);

    for (var i = 0; i < str.length ; i++) {
      buf[i] = str.charCodeAt(i);
    }

    console.log(buf);

    // node.js

### Buffer.isBuffer(obj)

Tests if `obj` is a `Buffer`.

'obj'가 'Buffer'인지 검사한다.

### Buffer.byteLength(string, encoding='utf8')

Gives the actual byte length of a string.  This is not the same as
`String.prototype.length` since that returns the number of *characters* in a
string.

문자열의 실제 바이트 길이를 돌려준다. 문자열에서 *캐릭터*의 수를 리턴하기 때문에 `String.prototype.length`와 동일하지 않다.

Example:

예제:

    str = '\u00bd + \u00bc = \u00be';

    console.log(str + ": " + str.length + " characters, " +
      Buffer.byteLength(str, 'utf8') + " bytes");

    // ½ + ¼ = ¾: 9 characters, 12 bytes


### buffer.length

The size of the buffer in bytes.  Note that this is not necessarily the size
of the contents. `length` refers to the amount of memory allocated for the
buffer object.  It does not change when the contents of the buffer are changed.

바이트에서 버퍼의 사이즈. 컨텐츠의 사이즈가 필수적이지 않다는 데 주의해라. 'length'는 버퍼객체에 할당된 메모리의 양을 참조하고 있다. 버퍼의 내용이 변경되었을 때 바꾸지 않는다.

    buf = new Buffer(1234);

    console.log(buf.length);
    buf.write("some string", "ascii", 0);
    console.log(buf.length);

    // 1234
    // 1234

### buffer.copy(targetBuffer, targetStart=0, sourceStart=0, sourceEnd=buffer.length)

Does copy between buffers. The source and target regions can be overlapped.

버퍼들간에 복사를 한다. 소스와 타켓가 같아도 된다.

Example: build two Buffers, then copy `buf1` from byte 16 through byte 19
into `buf2`, starting at the 8th byte in `buf2`.

예제: 2개의 버퍼를 만든 후 'buf1'의 16바이트부터 19바이트까지를 `buf2`의 8번째 바이트위치로 `buf2`로 복사한다. 

    buf1 = new Buffer(26);
    buf2 = new Buffer(26);

    for (var i = 0 ; i < 26 ; i++) {
      buf1[i] = i + 97; // 97 is ASCII a
      buf2[i] = 33; // ASCII !
    }

    buf1.copy(buf2, 8, 16, 20);
    console.log(buf2.toString('ascii', 0, 25));

    // !!!!!!!!qrst!!!!!!!!!!!!!


### buffer.slice(start, end=buffer.length)

Returns a new buffer which references the
same memory as the old, but offset and cropped by the `start` and `end`
indexes.

기존것과 같은 메모리를 차조하는 새로운 버퍼를 리턴하지만 `start`부터 `end` 인텍스까지만 잘려진다.

**Modifying the new buffer slice will modify memory in the original buffer!**

**새로운 버퍼 조각(slice)를 수정하는 것은 기존 버퍼의 메모리를 변경할 것이다!**

Example: build a Buffer with the ASCII alphabet, take a slice, then modify one byte
from the original Buffer.

예제: ASCII 알파벳으로 이루어진 버퍼를 만들고 슬라이스된 조각을 얻은 뒤 기존 버퍼에서 한 바이트를 수정한다.

    var buf1 = new Buffer(26);

    for (var i = 0 ; i < 26 ; i++) {
      buf1[i] = i + 97; // 97 is ASCII a
    }

    var buf2 = buf1.slice(0, 3);
    console.log(buf2.toString('ascii', 0, buf2.length));
    buf1[0] = 33;
    console.log(buf2.toString('ascii', 0, buf2.length));

    // abc
    // !bc
