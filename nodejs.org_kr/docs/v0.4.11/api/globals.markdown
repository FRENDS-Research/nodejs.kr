## 전역객체 (Global Objects)

These object are available in all modules. Some of these objects aren't
actually in the global scope but in the module scope - this will be noted.

이 객체들은 모든 모듈에서 이용할 수 있다. 사실 이들중 일부는 전역범위에 있지 않지만 모듈범위에는 있다. - 이것은 곧 알게 될 것이다.

###  global

The global namespace object.

전역 네임스페이스 객체

In browsers, the top-level scope is the global scope. That means that in
browsers if you're in the global scope `var something` will define a global
variable. In Node this is different. The top-level scope is not the global
scope; `var something` inside a Node module will be local to that module.

브라우저에서 최상위 범위는 전역범위이다. 브라우저의 전역범위에서 `var something`는 전역 변수로 정의된다는 것을 의미하지만 Node에서는 다르다. 최상위범위는 전역범위가 아니다: Node모듈내에서 `var something`는 모듈의 지역범위가 될 것이다.

### process

The process object. See the [process object](process.html#process) section.

프로세스 객체. [프로세스 객체](process.html#process) 부분을 참고하라.

### console

Used to print to stdout and stderr. See the [stdio](stdio.html) section.

stdout과 stderr를 프린트하려고 사용한다. [stdio](stdio.html) 부분을 참고하라.


### require()

To require modules. See the [Modules](modules.html#modules) section.
`require` isn't actually a global but rather local to each module.

모듈을 요청하기 위해 사용한다. [모듈](modules.html#modules) 부분을 봐라. `require`는 사실상 전역이라기 보다는 각 모듈의 지역범위이다.


### require.resolve()

Use the internal `require()` machinery to look up the location of a module,
but rather than loading the module, just return the resolved filename.

모듈의 위치를 검색하기 위해서 내부적으로 `require()`의 기능을 사용하지만 모듈을 로딩하지 않고 찾아된 파일명을 단순히 리턴한다.

### require.cache

Modules are cached in this object when they are required. By deleting a key
value from this object, the next `require` will reload the module.

모듈이 요청되었을때 이 객체안에 모듈들이 캐시된다. 이 객체에서 키값을 제거하면 다음번 `require`는 모듈을 다시 로딩할 것이다.

### require.paths

An array of search paths for `require()`.  This array can be modified to add
custom paths.

`require()`를 위한 검색 경로의 배열이다. 이 배열은 임의의 뎡로를 추가하기 위해서 수정될 수 있다.

Example: add a new path to the beginning of the search list

예제: 검색리스트의 앞부분에 새로운 경로를 추가한다

    require.paths.unshift('/usr/local/node');


### __filename

The filename of the script being executed.  This is the absolute path, and not necessarily
the same filename passed in as a command line argument.

실행된 스크립트의 파일명이다. 절대경로를 포함하고 있으며 커맨드라인의 아규먼트로 전달된 파일명과 반드시 같은 것은 아니다.

Example: running `node example.js` from `/Users/mjr`

예제: `/Users/mjr`에서 `node example.js`를 실행하였다.

    console.log(__filename);
    // /Users/mjr/example.js

`__filename` isn't actually a global but rather local to each module.

`__filename`는 사실상 전역이 아니라 각 모듈의 지역이다.

### __dirname

The dirname of the script being executed.

실행된 스크립트의 디렉토리 명.

Example: running `node example.js` from `/Users/mjr`

예제: `/Users/mjr`에서 `node example.js`를 실행하였다.

    console.log(__dirname);
    // /Users/mjr

`__dirname` isn't actually a global but rather local to each module.

`__dirname`는 사실상 전역이 아니라 각 모듈의 지역이다.


### module

A reference to the current module. In particular
`module.exports` is the same as the `exports` object. See `src/node.js`
for more information.
`module` isn't actually a global but rather local to each module.

현재 모듈에 대한 참조이다. 특별히 `module.exports`는 `exports` 객체와 동일하다. 더 자세히 알기를 원한다면 `src/node.js`를 봐라. `module`는  사실상 전역이 아니라 각 모듈의 지역이다.


### exports

An object which is shared between all instances of the current module and
made accessible through `require()`.
`exports` is the same as the `module.exports` object. See `src/node.js`
for more information.
`exports` isn't actually a global but rather local to each module.

현재 모듈과 `require()`를 통해서 접근가능하도록 만들어진 모든 인스턴스사이에 공유된 객체이다. `exports`는 `module.exports`객체와 동일하다. 더 자세히 알기를 원한다면 `src/node.js`를 봐라. `exports`는  사실상 전역이 아니라 각 모듈의 지역이다.

### setTimeout(cb, ms)
### clearTimeout(t)
### setInterval(cb, ms)
### clearInterval(t)

The timer functions are global variables. See the [timers](timers.html) section.

타이머 함수는 전역변수이다. [타이머](timers.html) 부분을 봐라.
