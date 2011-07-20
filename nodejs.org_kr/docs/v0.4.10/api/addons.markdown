## Addons

Addons are dynamically linked shared objects. They can provide glue to C and
C++ libraries. The API (at the moment) is rather complex, involving
knowledge of several libraries:
부가 기능은 동적으로 공유 객체에 연결된다. 그것들은 C, C++ 라이브러리를 위한 연결
지점을 제공한다. API는 일부 라이브러리의 지식을 포함하며, (현재) 매우 복잡하다.

 - V8 JavaScript, a C++ library. Used for interfacing with JavaScript:
   creating objects, calling functions, etc.  Documented mostly in the
   `v8.h` header file (`deps/v8/include/v8.h` in the Node source tree).
 - V8 자바스크립트 엔진은 C++ 라이브러리이다. JavaScript의 오브젝트를 만들거나
   함수 호출등의 인터페이스에 사용된다.  문서화 대부분은 `v8.h` 헤더파일에 기록되
   어 있다.  (Node 소스 트리상에서 `deps/v8/include/v8.h`)

 - libev, C event loop library. Anytime one needs to wait for a file
   descriptor to become readable, wait for a timer, or wait for a signal to
   received one will need to interface with libev.  That is, if you perform
   any I/O, libev will need to be used.  Node uses the `EV_DEFAULT` event
   loop.  Documentation can be found [here](http://cvs.schmorp.de/libev/ev.html).
 - libev는 C 이벤트 루프 라이브러리이다. 파일 기술자가 읽을 수 있게 되고, 타이머를 기다릴
   때 혹은 신호를 받기를 기다릴 때 등등 libev 인터페이스가 필요하다. 그것은 즉 어떤 I/O
   작업을 하려면 반드시 libev 을 써야 할 필요가 있다는 것이다. Node는 `EV_DEFAULT` 이벤트
   루프를 사용한다.  문서는 http:/cvs.schmorp.de/libev/ev.html [here] 에 있다.


 - libeio, C thread pool library. Used to execute blocking POSIX system
   calls asynchronously. Mostly wrappers already exist for such calls, in
   `src/file.cc` so you will probably not need to use it. If you do need it,
   look at the header file `deps/libeio/eio.h`.
 - libeio는 C 쓰레드 풀 라이브러리이다.  블럭킹 POSIX 시스템을 비동기적으로 실행하
   는데 사용한다.  이러한 호출을 위한 대부분의 래퍼는 이미 `src/file.cc`에 포함되어
   있기 때문에, 아마 그것을 사용하게 될 것이다.  필요한 경우, `deps/libeio/eio.h`
   헤더 파일을 참조하세요.

 - Internal Node libraries. Most importantly is the `node::ObjectWrap`
   class which you will likely want to derive from.
 - Node 내부 라이브러리에서 가장 중요한 것은 `node::ObjectWrap` 클래스이다.

 - Others. Look in `deps/` for what else is available.
 - 다른 것들의 사용법은 `deps/` 다음을 참조하세요.

Node statically compiles all its dependencies into the executable. When
compiling your module, you don't need to worry about linking to any of these
libraries.
Node는 런타임 의존적인 소스를 정적으로 컴파일한다. 모듈을 컴파일 할때 모든 라이브러리
들의 연결에 대해 아무런 주의도 필요하지 않는다.

To get started let's make a small Addon which does the following except in
C++:
먼저 작은 부가기능을 만들어 보기 위해서 C++에서 다음과 같이 동작하는 작은 기능을 만들어 보세요.

    exports.hello = 'world';

To get started we create a file `hello.cc`:
먼저 hello.cc 라는 파일을 만듭니다.

    #include <v8.h>

    using namespace v8;

    extern "C" void
    init (Handle<Object> target)
    {
      HandleScope scope;
      target->Set(String::New("hello"), String::New("world"));
    }

This source code needs to be built into `hello.node`, the binary Addon. To
do this we create a file called `wscript` which is python code and looks
like this:
이 소스 코드는 `hello.node`에 바이너리 부가기능으로 빌드가 필요하다. 빌드를 하기 위해서
는 `wscript` 라고 하는 다음과 같은 파이썬 코드를 생성한다.

    srcdir = '.'
    blddir = 'build'
    VERSION = '0.0.1'

    def set_options(opt):
      opt.tool_options('compiler_cxx')

    def configure(conf):
      conf.check_tool('compiler_cxx')
      conf.check_tool('node_addon')

    def build(bld):
      obj = bld.new_task_gen('cxx', 'shlib', 'node_addon')
      obj.target = 'hello'
      obj.source = 'hello.cc'

Running `node-waf configure build` will create a file
`build/default/hello.node` which is our Addon.
`node-waf configurae build`를 실행하면 `build/default/hello.node` 부가기능
파일이 만들어 진다.

`node-waf` is just [WAF](http://code.google.com/p/waf), the python-based build system. `node-waf` is
provided for the ease of users.
`node-waf` 는 http://code.google.com/p/waf/[WAF]에 있고 Python 기반의 빌드이다.
`node-waf` 는 사용자의 부담을 줄이기 위해서 제공되고 있다.

All Node addons must export a function called `init` with this signature:
Node의 모든 부가 기능들은 이 서명과 함께 `init` 함수 호출되도록 내보내야만 한다.

    extern 'C' void init (Handle<Object> target)

For the moment, that is all the documentation on addons. Please see
<https://github.com/ry/node_postgres> for a real example.
현재 추가된 문서는 이게 전부이고 실제 예제는 http://github.com/ry/node_postgres 를
참조하세요.