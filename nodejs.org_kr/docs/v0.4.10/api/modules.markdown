## 모듈 (Modules)

Node has a simple module loading system.  In Node, files and modules are in
one-to-one correspondence.  As an example, `foo.js` loads the module
`circle.js` in the same directory.

Node에는 간단한 모듈 로딩 시스템이 있다. Node에서 파일과 모듈은 1대 1 대응을 이룬다.
예를 들어 `foo.js` 는 같은 디렉토리에 있는 `circle.js` 모듈을 로드한다.

The contents of `foo.js`:

`foo.js`의 내용 :

    var circle = require('./circle.js');
    console.log( 'The area of a circle of radius 4 is '
               + circle.area(4));

The contents of `circle.js`:

`circle.js` 의 내용

    var PI = Math.PI;

    exports.area = function (r) {
      return PI * r * r;
    };

    exports.circumference = function (r) {
      return 2 * PI * r;
    };

The module `circle.js` has exported the functions `area()` and
`circumference()`.  To export an object, add to the special `exports`
object.

`circle.js`모듈은 `area()` 와 `circumference()` 함수를 내보낸다.  개체를 내보내기
위해서는 지정된 `exports` 객체에 추가해야 한다.

Variables
local to the module will be private. In this example the variable `PI` is
private to `circle.js`.

모듈내에 있는 변수는 private이 된다. 이 예제의 경우 변수 `PI`는 `circle.js`의 private 변수이다.

### 코어 모듈 (Core Modules)

Node has several modules compiled into the binary.  These modules are
described in greater detail elsewhere in this documentation.

Node에는 바이너리로 컴파일된 여러 모듈들이 있다. 이 모듈들에 관해서는 이 문서의 
다른 곳에서 더 자세하게 설명되어 있다.

The core modules are defined in node's source in the `lib/` folder.

코어 모듈들은 `lib/` 폴더에 있는 node 소스에 정의되어 있다.

Core modules are always preferentially loaded if their identifier is
passed to `require()`.  For instance, `require('http')` will always
return the built in HTTP module, even if there is a file by that name.

코어 모듈의 식별자가 `require()`에 전달되면 항상 코어모듈이 우선적으로 로드된다. 
예를 들어 같은 파일명이 있더라도 `require('http')`는 언제나 내장된 HTTP모듈을 리턴한다. 

### 파일 모듈 (File Modules)

If the exact filename is not found, then node will attempt to load the
required filename with the added extension of `.js`, and then `.node`.

일치하는 파일명을 찾지 못했다면 node는 `.js`확장자가 붙은 파일명을 로딩하려고 할 것이고
그래도 없으면 `.node`확장자의 파일을 찾는다.

`.js` files are interpreted as JavaScript text files, and `.node` files
are interpreted as compiled addon modules loaded with `dlopen`.

`.js` 파일들은 항상 자바스크립트 텍스트 파일로 인터프리팅되고 `.node`파일들은 `dlopen`로 로드되는 컴파일된 애드온 모듈로 인터프리팅된다. 

A module prefixed with `'/'` is an absolute path to the file.  For
example, `require('/home/marco/foo.js')` will load the file at
`/home/marco/foo.js`.

'/'`로 시작되는 모듈은 파일의 절대경로를 나타낸다. 예를 들어 `require('/home/marco/foo.js')`는 `/home/marco/foo.js` 파일을 로드할 것이다.

A module prefixed with `'./'` is relative to the file calling `require()`.
That is, `circle.js` must be in the same directory as `foo.js` for
`require('./circle')` to find it.

`'./'`로 시작되는 모듈은 `require()`를 호출하는 파일의 상대경로이다. `require('./circle')`에서 `circle.js`는 반드시 `foo.js`와 같은 디렉토리에 있어야 찾을 수 있다.

Without a leading '/' or './' to indicate a file, the module is either a
"core module" or is loaded from a `node_modules` folder.

'/'나 './'로 시작하지 않으면 모듈은 "코어 모듈"이거나 `node_modules`폴더에서 로드된 파일을 가리킨다.

### `node_modules`폴더에서의 로딩

If the module identifier passed to `require()` is not a native module,
and does not begin with `'/'`, `'../'`, or `'./'`, then node starts at the
parent directory of the current module, and adds `/node_modules`, and
attempts to load the module from that location.

`require()`에 전달된 모듈 식별자가 네이티브 모듈이 아니고 `'/'`, `'../'`, `'./'`로 시작되지 않았을 때 node는 현재 모듈의 부모 디렉토리에서 `/node_modules`를 찾아서 해당 위치에서 모듈을 로드하려고 시도한다.

If it is not found there, then it moves to the parent directory, and so
on, until the root of the tree is reached.

여기서도 발견되지 않는다면 부모 디렉토리로 이동하면서 루트경로까지 계속 반복한다.

For example, if the file at `'/home/ry/projects/foo.js'` called
`require('bar.js')`, then node would look in the following locations, in
this order:

예를 들어 `'/home/ry/projects/foo.js'`파일이 `require('bar.js')`를 호출하면 node는 다음의 경로들을 순서대로 찾을 것이다.

* `/home/ry/projects/node_modules/bar.js`
* `/home/ry/node_modules/bar.js`
* `/home/node_modules/bar.js`
* `/node_modules/bar.js`

This allows programs to localize their dependencies, so that they do not
clash.

이 방식은 프로그램들이 가진 의존성을 내부에 가지고 있을 수 있도록 해서 충돌나지 않게 한다.

### 모듈로써의 폴더

It is convenient to organize programs and libraries into self-contained
directories, and then provide a single entry point to that library.
There are three ways in which a folder may be passed to `require()` as
an argument.

내부에 있는 디렉토리에 프로그램과 라이브러리들을 구성하는 것은 편리하고 라이브러리에 대한 하나의 진입점을 제공한다. `require()`에 아규먼트로 폴더가 전달될 수 있는 3가지 방법이 있다. 

The first is to create a `package.json` file in the root of the folder,
which specifies a `main` module.  An example package.json file might
look like this:

첫번째는 폴더 루트경로에 `main` 모듈을 지정하는 `package.json`파일을 만드는 것이다. 예를 들어 package.json파일은 다음과 같이 될 수 있다.

    { "name" : "some-library",
      "main" : "./lib/some-library.js" }

If this was in a folder at `./some-library`, then
`require('./some-library')` would attempt to load
`./some-library/lib/some-library.js`.

이것이 `./some-library` 폴더에 있다면 `require('./some-library')`는 `./some-library/lib/some-library.js`파일을 로드하려고 할 것이다.

This is the extent of Node's awareness of package.json files.

이것이 package.json파일을 Node가 인식하는 범위이다.

If there is no package.json file present in the directory, then node
will attempt to load an `index.js` or `index.node` file out of that
directory.  For example, if there was no package.json file in the above
example, then `require('./some-library')` would attempt to load:

디렉토리에 package.json파일이 없다면 node는 `index.js`나 `index.node`파일을 로드하려고 할것이다. 예를 들어 위 예제에서 package.json파일이 없으면 `require('./some-library')`는 다음을 로드하려고 시도한다:

* `./some-library/index.js`
* `./some-library/index.node`

### 캐싱 (Caching)

Modules are cached after the first time they are loaded.  This means
(among other things) that every call to `require('foo')` will get
exactly the same object returned, if it would resolve to the same file.

모듈들은 최초 로드된 이후에 캐쉬된다. 이 말은 모든 `require('foo')`가 같은 파일로 로드될 때 완전히 같은 객체가 리턴된다는 것을 의미한다.

Multiple calls to `require('foo')` may not cause the module code to be
executed multiple times.  This is an important feature.  With it,
"partially done" objects can be returned, thus allowing transitive
dependencies to be loaded even when they would cause cycles.

`require('foo')`를 여러번 호출하더라도 모듈의 코드가 여러번 실행되는 것은 아니다. 이것은 중요하다. "일부분은 실행된" 객체가 리턴될 수 있기 때문에 타동 의존성(transitive dependencies)은 순환이 발생했을때도 로드된다.

If you want to have a module execute code multiple times, then export a
function, and call that function.

모듈의 코드가 여러번 실행되기를 원한다면 함수를 밖으로 전달하고 그 함수를 호출해라.

#### 모듈 캐싱 경고들 (Module Caching Caveats)

Modules are cached based on their resolved filename.  Since modules may
resolve to a different filename based on the location of the calling
module (loading from `node_modules` folders), it is not a *guarantee*
that `require('foo')` will always return the exact same object, if it
would resolve to different files.

모듈은 처리된 파일명에 기반해서 캐시된다. 모듈들은 모듈 호출(`node_modules`폴더에서 로딩되는)의 위치에 기반해서 다른 파일명으로 처리할 것이기 때문에 다른 파일로 처리되었다면 `require('foo')`가 항상 정확히 같은 객체를 리턴해 준다고 *보장*할 수 없다. 

### module.exports

The `exports` object is created by the Module system. Sometimes this is not
acceptable, many want their module to be an instance of some class. To do this
assign the desired export object to `module.exports`. For example suppose we
were making a module called `a.js`

`exports`객체는 Module 시스템에 의해서 생성된다. 종종 이것은 만족스럽지 않지만 많은 경우 모듈들이 어떤 클래스의 인스턴스가 되기를 원한다. 이것을 하기 위해서 `module.exports`에 외부에 전달하기를 원하는 객체를 할당한다. 예를 들어 `a.js`라는 모듈을 만들고 있다고 가정하자.

    var EventEmitter = require('events').EventEmitter;

    module.exports = new EventEmitter();

    // Do some work, and after some time emit
    // the 'ready' event from the module itself.
    // 어떤 일을 하고 얼마정도의 시간 후에 모듈자체적으로
    // 'ready'이벤트를 발생시킨다. 
    setTimeout(function() {
      module.exports.emit('ready');
    }, 1000);

Then in another file we could do

그 다음 다른 파일에서 다음처럼 할 수 있다.

    var a = require('./a');
    a.on('ready', function() {
      console.log('module a is ready');
    });


Note that assignment to `module.exports` must be done immediately. It cannot be
done in any callbacks.  This does not work:

`module.exports`로의 할당은 반드시 즉각적으로 수행되어야 한다는 것을 명심해라. 이것은 어떤 콜백안에서도 수행될 수 없다. 이것은 동작하지 않는다.

x.js:

    setTimeout(function() {
      module.exports = { a: "hello" };
    }, 0);

y.js:

    var x = require('./x');
    console.log(x.a);


### 모두 다 함께... (All Together...)

To get the exact filename that will be loaded when `require()` is called, use
the `require.resolve()` function.

`require()`가 호출되었을때 로드될 정확한 파일명을 얻기 위해서 `require.resolve()`함수를 사용한다.

Putting together all of the above, here is the high-level algorithm
in pseudocode of what require.resolve does:

위에서 본 모두 것들을 담고 있는 고차원 알고리즘이 require.resolve가 무엇을 하는지에 대한 수도코드에 있다.

    require(X) from module at path Y
    1. If X is a core module,
       a. return the core module
       b. STOP
    2. If X begins with './' or '/' or '../'
       a. LOAD_AS_FILE(Y + X)
       b. LOAD_AS_DIRECTORY(Y + X)
    3. LOAD_NODE_MODULES(X, dirname(Y))
    4. THROW "not found"

    Y 경로에 있는 모듈의 require(X)
    1. X가 코어 모듈이면,
        a. 코어모듈을 리턴한다.
        b. STOP
    2. X가 './'나 '/'나 '../'로 시작하면,
        a. LOAD_AS_FILE(Y + X)
        b. LOAD_AS_DIRECTORY(Y + X)
    3. LOAD_NODE_MODULES(X, dirname(Y))
    4. THROW "not found"

    LOAD_AS_FILE(X)
    1. If X is a file, load X as JavaScript text.  STOP
    2. If X.js is a file, load X.js as JavaScript text.  STOP
    3. If X.node is a file, load X.node as binary addon.  STOP

    LOAD_AS_FILE(X)
    1. X가 파일이면, 자바스크립트 텍스트로 X를 로드한다. STOP
    2. X.js가 파일이면, 자바스크립트 텍스트로 X를 로드한다. STOP
    3. X.node가 파일이면, 바이너리 애드온으로 X.node를 로드한다. STOP

    LOAD_AS_DIRECTORY(X)
    1. If X/package.json is a file,
       a. Parse X/package.json, and look for "main" field.
       b. let M = X + (json main field)
       c. LOAD_AS_FILE(M)
    2. LOAD_AS_FILE(X/index)

    LOAD_AS_DIRECTORY(X)
    1. X/package.json가 파일이면 ,
       a. X/package.json를 파신해서 "main"필드를 찾는다.
       b. let M = X + (json main field)
       c. LOAD_AS_FILE(M)
    2. LOAD_AS_FILE(X/index)

    LOAD_NODE_MODULES(X, START)
    1. let DIRS=NODE_MODULES_PATHS(START)
    2. for each DIR in DIRS:
       a. LOAD_AS_FILE(DIR/X)
       b. LOAD_AS_DIRECTORY(DIR/X)

    NODE_MODULES_PATHS(START)
    1. let PARTS = path split(START)
    2. let ROOT = index of first instance of "node_modules" in PARTS, or 0
    3. let I = count of PARTS - 1
    4. let DIRS = []
    5. while I > ROOT,
       a. if PARTS[I] = "node_modules" CONTINUE
       c. DIR = path join(PARTS[0 .. I] + "node_modules")
       b. DIRS = DIRS + DIR
       c. let I = I - 1
    6. return DIRS

### `require.paths`폴더로부터의 로딩

In node, `require.paths` is an array of strings that represent paths to
be searched for modules when they are not prefixed with `'/'`, `'./'`, or
`'../'`.  For example, if require.paths were set to:

node에서 `require.paths`는  `'/'`나 `'./'`나 `'../'로 시작하지 않았을 때 모듈을 찾아야하는 경로를 나타내는 문자열의 배열이다. 예를 들어 require.paths는 다음과 같이 되어있다면

    [ '/home/micheil/.node_modules',
      '/usr/local/lib/node_modules' ]

Then calling `require('bar/baz.js')` would search the following
locations:

`require('bar/baz.js')` 호출은 아래의 경로들을 찾을 것이다.

* 1: `'/home/micheil/.node_modules/bar/baz.js'`
* 2: `'/usr/local/lib/node_modules/bar/baz.js'`

The `require.paths` array can be mutated at run time to alter this
behavior.

`require.paths`배열은 이러한 동작을 바꾸기 위해서 런타임중에 변경될 수 있다.

It is set initially from the `NODE_PATH` environment variable, which is
a colon-delimited list of absolute paths.  In the previous example,
the `NODE_PATH` environment variable might have been set to:

절대경로의 콜론으로 구분된 리스트인 `NODE_PATH` 환경변수로 초기화된다. 이전 예제에서 `NODE_PATH`환경변수는 다음과 같이 설정되었을 것이다.

    /home/micheil/.node_modules:/usr/local/lib/node_modules

Loading from the `require.paths` locations is only performed if the
module could not be found using the `node_modules` algorithm above.
Global modules are lower priority than bundled dependencies.

앞에서 본 `node_modules` 알고리즘을 사용해서 모듈을 찾지 못했을 때만 `require.paths`의 위치들로부터의 로딩이 수행된다. 전역 모듈들은 포함되어 있는 의존성들보다 우선순위가 낮다.

#### **Note:** Please Avoid Modifying `require.paths`

#### **주의점:** `require.paths`를 수정하는 것은 피해라

`require.paths` may disappear in a future release.

`require.paths`는 차후버전에서는 없어질 수 있다.

While it seemed like a good idea at the time, and enabled a lot of
useful experimentation, in practice a mutable `require.paths` list is
often a troublesome source of confusion and headaches.

그 당시에는 괜찮은 아이디어로 보였고 많은 유용한 실험을 가능하게 해주었지만 실제 사용에서 변경가능한 `require.paths` 리스트는 종종 혼동과 걱정거리를 주는 말썽많은 소스였다.

##### Setting `require.paths` to some other value does nothing.

##### `require.paths`를 다른 값으로 설정하는 것은 아무일도 일어나지 않는다.

This does not do what one might expect:

기대할 수 있는 다음과 같은 것은 수행되지 않는다:

    require.paths = [ '/usr/lib/node' ];

All that does is lose the reference to the *actual* node module lookup
paths, and create a new reference to some other thing that isn't used
for anything.

*실제로* 노드 모듈을 찾는 경로에 대한 참조를 읽어버리고 어떤 것에도 사용되지 않는 다른 것에 대한 새로운 참조를 생성하는 것이 전부이다.

##### Putting relative paths in `require.paths` is... weird.

##### `require.paths`에 상대경로를 두는 것은 이상하다.

If you do this:

만약 다음과 같이 하였다면:

    require.paths.push('./lib');

then it does *not* add the full resolved path to where `./lib`
is on the filesystem.  Instead, it literally adds `'./lib'`,
meaning that if you do `require('y.js')` in `/a/b/x.js`, then it'll look
in `/a/b/lib/y.js`.  If you then did `require('y.js')` in
`/l/m/n/o/p.js`, then it'd look in `/l/m/n/o/lib/y.js`.

파일시스템에서 `./lib`로 처리된 전체경로가 추가되는 일은 *일어나지 않는다.* 대신에 글자그대로 `'./lib'`가 추가되고 이것은 `/a/b/x.js`에서 `require('y.js')`를 사용하였을 때 `/a/b/lib/y.js`를 찾을 것이다. `/l/m/n/o/p.js`에서 `require('y.js')`를 사용하면 `/l/m/n/o/lib/y.js`를 찾을 것이다.

In practice, people have used this as an ad hoc way to bundle
dependencies, but this technique is brittle.

연습할 때, 사람들은 번들화 된 의존성은 애드훅하는 방법으로  이것을 사용하곤 했지만 이 기술은 다루기 힘든 것이다.

##### 격리없음 (Zero Isolation)

There is (by regrettable design), only one `require.paths` array used by
all modules.

모든 모듈들에서 사용되는 오직 단 하나의 `require.paths`배열만이 있다.(후회되는 디자인이다.)

As a result, if one node program comes to rely on this behavior, it may
permanently and subtly alter the behavior of all other node programs in
the same process.  As the application stack grows, we tend to assemble
functionality, and those parts interact in ways that are difficult to
predict.

그 결과로 하나의 노드 프로그램이 이러한 동작에 의존하면 그것은 영구적으로 갑작스럽게 같은 프로세스의 다른 모든 노드프로그램들의 동작을 바꿀수 있다.

### 메인 모듈에 접근하기 (Accessing the main module)

When a file is run directly from Node, `require.main` is set to its
`module`. That means that you can determine whether a file has been run
directly by testing

Node로 파일을 직접 실행했을 때 `require.main`은 그것의 `module`로 설정된다. 이것은 테스트를 통해 파일이 직접 실행되었는지 아닌지를 알 수 있다는 의미이다.

    require.main === module

For a file `foo.js`, this will be `true` if run via `node foo.js`, but
`false` if run by `require('./foo')`.

`foo.js`파일에 대해서 `node foo.js`로 실행하면  `true`이지만 `require('./foo')`로 실행하면 `false`가 된다.

Because `module` provides a `filename` property (normally equivalent to
`__filename`), the entry point of the current application can be obtained
by checking `require.main.filename`.

`moduels`가 `filename`프로퍼티(일반적으로 `__filename`와 동일하다)를 제공하기 때문에 현재 애플리케이션에서  `require.main.filename`를 체크해서 얻을 수 있다.

## 부록 : 패키지 매니저 팁

The semantics of Node's `require()` function were designed to be general
enough to support a number of sane directory structures. Package manager
programs such as `dpkg`, `rpm`, and `npm` will hopefully find it possible to
build native packages from Node modules without modification.

`require()`함수의 시맨틱은  다수의 정상적인 디렉토리 구조를 충분히 지원할 수 있도록 디자인되었다. `dpkg`, `rpm`, `npm`같은 팩키지 매니저 프로그램들은 수정없이 Node 모듈에서 네이티브 팩키지를 빌드할 수 있는 것을 찾기를 바란다. 

Below we give a suggested directory structure that could work:

아래는 작업할 디렉토리 구조를 보여주고 있다:

Let's say that we wanted to have the folder at
`/usr/lib/node/<some-package>/<some-version>` hold the contents of a
specific version of a package.

팩키지의 특정버전을 가지고 있는 `/usr/lib/node/<some-package>/<some-version>`폴더를 가지기 원한다고 해보자.

Packages can depend on one another. In order to install package `foo`, you
may have to install a specific version of package `bar`.  The `bar` package
may itself have dependencies, and in some cases, these dependencies may even
collide or form cycles.

팩키지는 또다른 무언가에 의존할 수 있다. `foo` 팩키지를 설치하기 위해서 `bar`팩키지의 특정버전을 설치해야 할 것이다. `bar`팩키지는 자체적으로 의존성을 가지고 있고 어떤 경우에는 이런한 의존성은 충돌하거나 순환의존성을 가질 수 있다.

Since Node looks up the `realpath` of any modules it loads (that is,
resolves symlinks), and then looks for their dependencies in the
`node_modules` folders as described above, this situation is very simple to
resolve with the following architecture:

Node가 로드되는 어떤 모듈의  `실제경로`찾기 때문에(아마 심볼릭 링크로 처리될 것이다) 앞에서 설명한 `node_modules`에서 의존 라이브러리들을 찾는다. 이 상황은 아래의 구조에서 아주 간단하게 처리된다.

* `/usr/lib/node/foo/1.2.3/` - Contents of the `foo` package, version 1.2.3.
* `/usr/lib/node/bar/4.3.2/` - Contents of the `bar` package that `foo`
  depends on.
* `/usr/lib/node/foo/1.2.3/node_modules/bar` - Symbolic link to
  `/usr/lib/node/bar/4.3.2/`.
* `/usr/lib/node/bar/4.3.2/node_modules/*` - Symbolic links to the packages
  that `bar` depends on.

* `/usr/lib/node/foo/1.2.3/` - `foo` 팩키지, 버전 1.2.3의 내용.
* `/usr/lib/node/bar/4.3.2/` - `foo`가 의존하는 `bar`팩키지의 내용.
* `/usr/lib/node/foo/1.2.3/node_modules/bar` - `/usr/lib/node/bar/4.3.2/`에 대한 심볼릭 링크.
* `/usr/lib/node/bar/4.3.2/node_modules/*` - `bar`가 의존하는 팩키지에 대한 심볼릭 링크.

Thus, even if a cycle is encountered, or if there are dependency
conflicts, every module will be able to get a version of its dependency
that it can use.

그러므로  순환 의존이 발생하거나 의존성 충돌이 발생하더라도 모든 모듈들이 자신이 사용하는 의존 모듈의 버전을 가질 수 있을 것이다.

When the code in the `foo` package does `require('bar')`, it will get the
version that is symlinked into `/usr/lib/node/foo/1.2.3/node_modules/bar`.
Then, when the code in the `bar` package calls `require('quux')`, it'll get
the version that is symlinked into
`/usr/lib/node/bar/4.3.2/node_modules/quux`.

`foo`팩키지의 코드가 `require('bar')`를 수행했을 때 `/usr/lib/node/foo/1.2.3/node_modules/bar`에서 심볼릭링크된 버전을 얻을 것이다. 그 다음에 `bar`팩키지의 코드가 `require('quux')`를 호출했을 때 `/usr/lib/node/bar/4.3.2/node_modules/quux`에서 심볼릭 링크된 버전을 얻을 것이다.

Furthermore, to make the module lookup process even more optimal, rather
than putting packages directly in `/usr/lib/node`, we could put them in
`/usr/lib/node_modules/<name>/<version>`.  Then node will not bother
looking for missing dependencies in `/usr/node_modules` or `/node_modules`.

게다가 더욱 최적의 모듈 검색 프로세스를 만들기 위해서 `/usr/lib/node`안에 직접 팩키지들을 두기 보다는 `/usr/lib/node_modules/<name>/<version>`안에 필티지들을 둘 수 있습니다. 그 다음 node는 `/usr/node_modules`나 `/node_modules`에서 빠진 의존성을 찾기위해서 귀찮게 하지 않을 것이다.

In order to make modules available to the node REPL, it might be useful to
also add the `/usr/lib/node_modules` folder to the `$NODE_PATH` environment
variable.  Since the module lookups using `node_modules` folders are all
relative, and based on the real path of the files making the calls to
`require()`, the packages themselves can be anywhere.

node REPL에서 모듈을 이용하기 위해서  `$NODE_PATH`환경변수에 `/usr/lib/node_modules` 폴터를 추가하는 것은 유용할 것이다. `node_modules`폴더를 사용한 모듈 겸색은 모두 상대적이고 `require()`를 호출하는 파일의 실제 경로에 기반하기 때문에 팩키지들은 어느 곳에나 위치할 수 있다.
