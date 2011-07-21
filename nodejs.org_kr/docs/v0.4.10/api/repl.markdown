## REPL

A Read-Eval-Print-Loop (REPL) is available both as a standalone program and easily
includable in other programs.  REPL provides a way to interactively run
JavaScript and see the results.  It can be used for debugging, testing, or
just trying things out.
Read-Eval-Print-Loop (PEPL)은 독립실행형 프로그램과 손쉽게 다른 프로그램을 통합하여 이용할
수 있다.  REPL 은 인터렉티브하게 JavaScript를 실행하고 결과를 볼 수 있는 방법을 제공한다.
이것은 디버깅, 테스팅 혹은 기타 여러가지를 시도하는 용도로 사용된다.

By executing `node` without any arguments from the command-line you will be
dropped into the REPL. It has simplistic emacs line-editing.
명렬줄에서 인수 없이 `node`를 실행하면 PEPL 프로그램 시스템으로 진입한다.  PEPL은
단순한 Emacs 라인 에디터이다.

    mjr:~$ node
    Type '.help' for options.
    > a = [ 1, 2, 3];
    [ 1, 2, 3 ]
    > a.forEach(function (v) {
    ...   console.log(v);
    ...   });
    1
    2
    3

For advanced line-editors, start node with the environmental variable `NODE_NO_READLINE=1`.
This will start the REPL in canonical terminal settings which will allow you to use with `rlwrap`.
더욱 진보된 라인 에디터를 위해서 환경변수에 `NODE_NO_READLINE=1`를 설정하고 node를 실행한다.
따라서 표준 터미널 설정의 REPL 을 실행하고 `rlwrap` 사용 상태에서 REPL을 이용할 수 있다.

For example, you could add this to your bashrc file:
예를 들어, bashrc 파일에 다음과 같이 설정을 추가한다.

    alias node="env NODE_NO_READLINE=1 rlwrap node"


### repl.start(prompt='> ', stream=process.stdin)

Starts a REPL with `prompt` as the prompt and `stream` for all I/O.  `prompt`
is optional and defaults to `> `.  `stream` is optional and defaults to
`process.stdin`.
프롬프트 기호와 같은 `prompt`와 모든 I/O `stream`을 가지고 시작한다. `prompt`는 생략
가능하며 기본값은 `> `입니다.  `stream`은 선택사항이고 기본값은 `process.openStdin`이다.

Multiple REPLs may be started against the same running instance of node.  Each
will share the same global object but will have unique I/O.
여러 REPL을 실행하면 동일한 node 인스턴스가 실행되지 않을 수 있다. 각 REPL은 전역 객체
를 공유하지만 유일한 I/O를 갖는다.

Here is an example that starts a REPL on stdin, a Unix socket, and a TCP socket:
REPL 표준 입력, Unix 도메인 소켓, TCP 소켓하에 시작한다.

    var net = require("net"),
        repl = require("repl");

    connections = 0;

    repl.start("node via stdin> ");

    net.createServer(function (socket) {
      connections += 1;
      repl.start("node via Unix socket> ", socket);
    }).listen("/tmp/node-repl-sock");

    net.createServer(function (socket) {
      connections += 1;
      repl.start("node via TCP socket> ", socket);
    }).listen(5001);

Running this program from the command line will start a REPL on stdin.  Other
REPL clients may connect through the Unix socket or TCP socket. `telnet` is useful
for connecting to TCP sockets, and `socat` can be used to connect to both Unix and
TCP sockets.
이 프로그램은 명령줄에서 실행하면 표준 입력에 따라 REPL이 시작된다. 다른 REPL 클라이언트는
UNIX 도메인 소켓 또는 TCP 소켓을 통해 연결할 수 있다. `telnet`은 TCP 소켓 연결에 유용하다.
`socat`은 UNIX와 TCP 소켓에 연결하기 위해 사용할 수 있다.

By starting a REPL from a Unix socket-based server instead of stdin, you can
connect to a long-running node process without restarting it.
표준 입력 대신에 UNIX 도메인 소켓을 기반으로 한 서버에서 REPL을 시작하고 다시 시작하지
않고 node 상주 프로세스에 연결할 수 있다.


### REPL Features

Inside the REPL, Control+D will exit.  Multi-line expressions can be input.
REPL에서 Control+D를 하면 종료한다.  여러 줄의 표현식을 입력할 수 있다.

The special variable `_` (underscore) contains the result of the last expression.
특별한 변수 `_` (밑줄)은 맨 마지막 표현식의 결과를 유지한다.

    > [ "a", "b", "c" ]
    [ 'a', 'b', 'c' ]
    > _.length
    3
    > _ += 1
    4

The REPL provides access to any variables in the global scope. You can expose a variable
to the REPL explicitly by assigning it to the `context` object associated with each
`REPLServer`.  For example:
REPL은 전역에 존재하는 모든 변수에 접근할 수 있다. 각 `REPLServer`에 관련된 `context` 객체를
부여하여 명시적으로 변수를 노출시킬 수 있다.

    // repl_test.js
    var repl = require("repl"),
        msg = "message";

    repl.start().context.m = msg;

Things in the `context` object appear as local within the REPL:
`context` 개체에 설정된 변수는 REPL안에 로컬 변수로 나타날 것이다.

    mjr:~$ node repl_test.js
    > m
    'message'

There are a few special REPL commands:
REPL은 몇 가지 특별한 명령이 있다.

  - `.break` - While inputting a multi-line expression, sometimes you get lost
    or just don't care about completing it. `.break` will start over.
  - `.break` - 여러 행에 걸쳐 수식을 입력하는 동안 때로 중간에 잃어 버리거나 수식을 완료하지 않더라도
  `.break`는 처음부터 다시 시작한다.
  - `.clear` - Resets the `context` object to an empty object and clears any multi-line expression.
  - `.clear` - `context` 객체를 빈 상태로 리셋하고, 여러 줄로 입력한 수식을 지운다.
  - `.exit` - Close the I/O stream, which will cause the REPL to exit.
  - `.exit` - I/O 스트림을 닫고 REPL을 종료한다.
  - `.help` - Show this list of special commands.
  - `.help` - 명령 목록을 표시한다.

