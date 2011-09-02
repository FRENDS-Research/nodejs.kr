## Crypto

Use `require('crypto')` to access this module.

`require('crypto')`를 사용해서 모듈에 접근한다.

The crypto module requires OpenSSL to be available on the underlying platform.
It offers a way of encapsulating secure credentials to be used as part
of a secure HTTPS net or http connection.

기반플랫폼에서 crypto모듈을 이용하려면 OpenSSL이 필요하다. HTTPS나 HTTP 보안연결로 사용하기 위한 보안자격을 은닉화하는 방법을 제공한다.

It also offers a set of wrappers for OpenSSL's hash, hmac, cipher, decipher, sign and verify methods.

그리고 OpenSSL의 hash, hmac, cipher, decipher, sign, verify 메서드를 랩핑해서 제공한다.

### crypto.createCredentials(details)

Creates a credentials object, with the optional details being a dictionary with keys:

자격객체를 생성하고 이 객체는 다음과 같은 키를 가진 딕션어리를 가질 수 있다:

* `key` : a string holding the PEM encoded private key
* `cert` : a string holding the PEM encoded certificate
* `ca` : either a string or list of strings of PEM encoded CA certificates to trust.

* `key` : PEM으로 인코딩된 프라이핏 키를 가지는 문자열
* `cert` : PEM으로 인코딩된 증명서를 가지는 문자열
* `ca` : PEM으로 인코딩된 신뢰할 수 있는 CA 자격증의 문자열이거나 문자열의 리스트

If no 'ca' details are given, then node.js will use the default publicly trusted list of CAs as given in
<http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>.

'ca'가 없다면 node.js는 <http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>에 있는 CA의 신뢰할 수 있는 공개리스트를 기본적으로 사용할 것이다.


### crypto.createHash(algorithm)

Creates and returns a hash object, a cryptographic hash with the given algorithm
which can be used to generate hash digests.

해쉬다이제스트를 생성하는데 사용되는 algorithm으로 암호화된 해쉬객체를 생성해서 리턴한다.

`algorithm` is dependent on the available algorithms supported by the version
of OpenSSL on the platform. Examples are `'sha1'`, `'md5'`, `'sha256'`, `'sha512'`, etc.
On recent releases, `openssl list-message-digest-algorithms` will display the available digest algorithms.

`알고리즘`은 플랫폼상의 OpenSSL 버전에 따른 알고리즘에 의존한다. `'sha1'`, `'md5'`, `'sha256'`, `'sha512'`등이 그 예이다. 최근 버전에서는 `openssl list-message-digest-algorithms`를 통해서 가능한 다이제스트 알고리즘을 볼 수 있다.

Example: this program that takes the sha1 sum of a file

예 : 파일의 sha1 합을 구하는 프로그램

    var filename = process.argv[2];
    var crypto = require('crypto');
    var fs = require('fs');

    var shasum = crypto.createHash('sha1');

    var s = fs.ReadStream(filename);
    s.on('data', function(d) {
      shasum.update(d);
    });

    s.on('end', function() {
      var d = shasum.digest('hex');
      console.log(d + '  ' + filename);
    });

### hash.update(data)

Updates the hash content with the given `data`.
This can be called many times with new data as it is streamed.

`data`로 해쉬내용을 갱신한다.
스트림되는 새로운 데이터로 여러번 호출될 수 있다.

### hash.digest(encoding='binary')

Calculates the digest of all of the passed data to be hashed.
The `encoding` can be `'hex'`, `'binary'` or `'base64'`.

전달받은 해쉬데이터의 다이제스트를 계산한다.
`encoding`에는 `'hex'`, `'binary'`, `'base64'`가 가능하다.

Note: `hash` object can not be used after `digest()` method been called.

Note: `digest()`메서드 호출뒤에는 `hash` 객체를 사용할 수 없다.


### crypto.createHmac(algorithm, key)

Creates and returns a hmac object, a cryptographic hmac with the given algorithm and key.

전달받은 algorithm과 key로 암호화된 hmac 객체를 생성해서 리턴한다.

`algorithm` is dependent on the available algorithms supported by OpenSSL - see createHash above.
`key` is the hmac key to be used.

`algorithm`은 OpenSSL에서 지원되는 알고리즘에 의존한다. - createHash를 봐라
`key`는 hmac키로 사용된다.

### hmac.update(data)

Update the hmac content with the given `data`.
This can be called many times with new data as it is streamed.

전달받은 `data`로 hmac 내용을 갱신한다.
스트림되는 새로운 데이터로 여러번 호출될 수 있다.

### hmac.digest(encoding='binary')

Calculates the digest of all of the passed data to the hmac.
The `encoding` can be `'hex'`, `'binary'` or `'base64'`.

전달받은 해쉬데이터의 다이제스트를 계산한다.
`encoding`에는 `'hex'`, `'binary'`, `'base64'`가 가능하다.

Note: `hmac` object can not be used after `digest()` method been called.

Note: `digest()`메서드 호출뒤에는 `hmac` 객체를 사용할 수 없다.


### crypto.createCipher(algorithm, password)

Creates and returns a cipher object, with the given algorithm and password.

전달받은 algorithm과 password로 암호화된 cipher 객체를 생성해서 리턴한다.

`algorithm` is dependent on OpenSSL, examples are `'aes192'`, etc.
On recent releases, `openssl list-cipher-algorithms` will display the
available cipher algorithms.
`password` is used to derive key and IV, which must be `'binary'` encoded
string (See the [Buffers](buffers.html) for more information).

`알고리즘`은 OpenSSL에 의존적이고 `'aes192'`등이 있다.
최신 버전에선 `openssl list-cipher-algorithms`로 가능한 cipher 알고리즘을 볼 수 있다.
`'binary'`로 인코딩된 문자열인 key와 IV를 얻기위해서 `password`를 사용한다.

### crypto.createCipheriv(algorithm, key, iv)

Creates and returns a cipher object, with the given algorithm, key and iv.

전달받은 algorithem, key, iv로 cipher 객체를 생성해서 리턴한다.

`algorithm` is the same as the `createCipher()`. `key` is a raw key used in
algorithm. `iv` is an Initialization vector. `key` and `iv` must be `'binary'`
encoded string (See the [Buffers](buffers.html) for more information).

`algorithm`은 `createCipher()`와 같다. `key`는 알고리즘에서 사용되는 로우키이다. `iv`는 초기화 벡터이다. `key`와 `iv`는 반드시 `'binary'`로 인코딩된 문자열이어야 한다.(더 자세한 내용은 [Buffers](buffers.html) 참조)

### cipher.update(data, input_encoding='binary', output_encoding='binary')

Updates the cipher with `data`, the encoding of which is given in `input_encoding`
and can be `'utf8'`, `'ascii'` or `'binary'`. The `output_encoding` specifies
the output format of the enciphered data, and can be `'binary'`, `'base64'` or `'hex'`.

`input_encoding`으로 인코딩된 `data`로 cipher를 갱신한다. `input_encoding`는 `'utf8'`, `'ascii'`, `'binary'`가 가능하다. `output_encoding`는 암호화된 데이터의 포맷이고 `'binary'`, `'base64'`, `'hex'`가 가능하다

Returns the enciphered contents, and can be called many times with new data as it is streamed.

암호화된 내용을 리턴하고 스트림되는 새로운 데이터로 여러번 호출될 수 있다.

### cipher.final(output_encoding='binary')

Returns any remaining enciphered contents, with `output_encoding` being one of: `'binary'`, `'base64'` or `'hex'`.

`output_encoding`로 암호화된 내용중 남아있는 모두를 리턴한다. `output_encoding`는 `'binary'`, `'base64'`, `'hex'`가 가능하다.

Note: `cipher` object can not be used after `final()` method been called.

Node: `final()`메서드가 호출된 후에는 `cipher`객체를 사용할 수 없다.


### crypto.createDecipher(algorithm, password)

Creates and returns a decipher object, with the given algorithm and key.
This is the mirror of the [createCipher()](#crypto.createCipher) above.

전달받은 algorithm과 key로 복호화된 객체를 생성해서 리턴한다.
앞에서 본 [createCipher()](#crypto.createCipher)의 반대이다.

### crypto.createDecipheriv(algorithm, key, iv)

Creates and returns a decipher object, with the given algorithm, key and iv.
This is the mirror of the [createCipheriv()](#crypto.createCipheriv) above.

전달받은 algorithm, key, iv로 복호화한 객체를 생성해서 리턴한다.
앞에서 본 [createCipheriv()](#crypto.createCipheriv)의 반대이다.

### decipher.update(data, input_encoding='binary', output_encoding='binary')

Updates the decipher with `data`, which is encoded in `'binary'`, `'base64'` or `'hex'`.
The `output_decoding` specifies in what format to return the deciphered plaintext: `'binary'`, `'ascii'` or `'utf8'`.

`'binary'`나 `'base64'`, `'hex'`로 인코딩된 `data`로 decipher를 갱신한다.
`output_decoding`는 복호화된 평문의 포맷으로 `'binary'`나 `'ascii'`, `'utf8'`가 가능하다.

### decipher.final(output_encoding='binary')

Returns any remaining plaintext which is deciphered,
with `output_encoding` being one of: `'binary'`, `'ascii'` or `'utf8'`.

`'binary'`나 `'ascii'`, `'utf8'`중 하나인 `output_encoding`로 복호화된 평문중 남아있는 모두를 리턴한다.

Note: `decipher` object can not be used after `final()` method been called.

Note: `final()`메서드 호출뒤에는 `decipher`객체를 사용할 수 없다.


### crypto.createSign(algorithm)

Creates and returns a signing object, with the given algorithm.
On recent OpenSSL releases, `openssl list-public-key-algorithms` will display
the available signing algorithms. Examples are `'RSA-SHA256'`.

전달받은 algorithm으로 서명된 객체를 생성해서 리턴한다.
최신 OpenSSL에서는 `openssl list-public-key-algorithms`로 가능한 서명 알고리즘을 볼수 있고 그 예로 `'RSA-SHA256'`가 있다.

### signer.update(data)

Updates the signer object with data.
This can be called many times with new data as it is streamed.

data로 signer 객체를 갱신한다.
스트립되는 새로운 데이터로 여러번 호출될 수 있다.

### signer.sign(private_key, output_format='binary')

Calculates the signature on all the updated data passed through the signer.
`private_key` is a string containing the PEM encoded private key for signing.

signer로 전달되는 갱신데이터의 모든 서명을 계산한다.
`private_key`는 서명에 사용되는 PEM으로 인코딩된 프라이빗 키 문자열이다.

Returns the signature in `output_format` which can be `'binary'`, `'hex'` or `'base64'`.

`'binary'`나 `'hex'`, `'base64'`중 하나인 `output_format`으로 서명을 리턴한다.

Note: `signer` object can not be used after `sign()` method been called.

Note: `sign()`메서드가 호출되면 `signer`객체를 사용할 수 없다.


### crypto.createVerify(algorithm)

Creates and returns a verification object, with the given algorithm.
This is the mirror of the signing object above.

전달받은 algoritm으로 증명 객체를 생성해서 리턴한다.
앞서 본 signing 객체의 반대이다.

### verifier.update(data)

Updates the verifier object with data.
This can be called many times with new data as it is streamed.

data로 verifier 객체를 갱신한다.
스트림되는 새로운 데이터로 여러번 호출될 수 있다.

### verifier.verify(object, signature, signature_format='binary')

Verifies the signed data by using the `object` and `signature`. `object` is  a
string containing a PEM encoded object, which can be one of RSA public key,
DSA public key, or X.509 certificate. `signature` is the previously calculated
signature for the data, in the `signature_format` which can be `'binary'`,
`'hex'` or `'base64'`.

`object`와 `signature`로 서명된 데이터를 증명한다. `object`는 PEM으로 인코딩된 객체를 담고 있는 문자열이다. 이는 RSA 공개키나 DSA 공개키, X.509 증명서가 될 수 있다. `signature`는 data에서 앞서 계산된 서명이다. `signature_format`은 `'binary'`나 `'hex'`, `'base64'`가 될 수 있다.

Returns true or false depending on the validity of the signature for the data and public key.

data와 공개키에 대한 서명의 유효성에 따라 true나 false를 리턴한다.

Note: `verifier` object can not be used after `verify()` method been called.

Note: `verify()`메서드 호출 후에는 `verifier`객체를 사용할 수 없다.

### crypto.createDiffieHellman(prime_length)

Creates a Diffie-Hellman key exchange object and generates a prime of the
given bit length. The generator used is `2`.

Diffie-Hellman key exchange 객체를 생성하고 전달받은  bit 길이만큼의 소수를 생성한다. 사용되는 제너레이터는 `2`이다.

### crypto.createDiffieHellman(prime, encoding='binary')

Creates a Diffie-Hellman key exchange object using the supplied prime. The
generator used is `2`. Encoding can be `'binary'`, `'hex'`, or `'base64'`.

전달받은 소스로 Diffie-Hellman key exchange 객체를 생성한다. 사용되는 제너레이터는 `2`이다. 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 될 수 있다.

### diffieHellman.generateKeys(encoding='binary')

Generates private and public Diffie-Hellman key values, and returns the
public key in the specified encoding. This key should be transferred to the
other party. Encoding can be `'binary'`, `'hex'`, or `'base64'`.

공개/비공개 Diffie-Hellman 키값을 생성하고 명시된 인코딩으로 공개키를 리턴한다. 상대방에게 이 키를 전송해야 한다. 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 될 수 있다.

### diffieHellman.computeSecret(other_public_key, input_encoding='binary', output_encoding=input_encoding)

Computes the shared secret using `other_public_key` as the other party's
public key and returns the computed shared secret. Supplied key is
interpreted using specified `input_encoding`, and secret is encoded using
specified `output_encoding`. Encodings can be `'binary'`, `'hex'`, or
`'base64'`. If no output encoding is given, the input encoding is used as
output encoding.

상대방의 공캐키로 `other_public_key`를 사용해서 공유된 시크립을 계산하고 계산된 공유 시크릿을 리턴한다. 명시된 `input_encoding`로 전달된 키를 인터프리트하고 시크릿은 `output_encoding`로 인코딩한다. 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 될 수 있다. output_encoding이 없다면 input_encoding을 사용한다.

### diffieHellman.getPrime(encoding='binary')

Returns the Diffie-Hellman prime in the specified encoding, which can be
`'binary'`, `'hex'`, or `'base64'`.

지정된 인코딩으로 Diffie-Hellman 소수를 리턴하고 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 가능하다.

### diffieHellman.getGenerator(encoding='binary')

Returns the Diffie-Hellman prime in the specified encoding, which can be
`'binary'`, `'hex'`, or `'base64'`.

지정된 인코딩으로 Diffie-Hellman 제너레이터를 리턴하고 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 가능하다.

### diffieHellman.getPublicKey(encoding='binary')

Returns the Diffie-Hellman public key in the specified encoding, which can
be `'binary'`, `'hex'`, or `'base64'`.

지정된 인코딩으로 Diffie-Hellman 공개키를 리턴하고 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 가능하다.

### diffieHellman.getPrivateKey(encoding='binary')

Returns the Diffie-Hellman private key in the specified encoding, which can
be `'binary'`, `'hex'`, or `'base64'`.

지정된 인코딩으로 Diffie-Hellman 비공개키를 리턴하고 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 가능하다.

### diffieHellman.setPublicKey(public_key, encoding='binary')

Sets the Diffie-Hellman public key. Key encoding can be `'binary'`, `'hex'`,
or `'base64'`.

Diffie-Hellman 공캐키를 설정한다. 키 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 가능하다.

### diffieHellman.setPrivateKey(public_key, encoding='binary')

Sets the Diffie-Hellman private key. Key encoding can be `'binary'`, `'hex'`, or `'base64'`.

Diffie-Hellman 비공캐키를 설정한다. 키 인코딩은 `'binary'`나 `'hex'`, `'base64'`가 가능하다.

### pbkdf2(password, salt, iterations, keylen, callback)

Asynchronous PBKDF2 applies pseudorandom function HMAC-SHA1 to derive
a key of given length from the given password, salt and iterations.
The callback gets two arguments `(err, derivedKey)`.

비동기 PBKDF2는 passwrod, salt, iterations에서 주어진 길이의 키를 얻기 기 위해서 의사난수(pseudorandom)함수 HMAC-SHA1를 사용한다. callback은 `(err, derivedKey)` 2개의 아규먼트를 갖는다.