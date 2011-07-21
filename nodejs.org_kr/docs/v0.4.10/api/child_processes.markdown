## Child Processes

Node provides a tri-directional `popen(3)` facility through the `ChildProcess`
class.
Node는 `ChildProcess`클래스를 통해 3방향 `popen(3)` 기능을 제공한다.

It is possible to stream data through the child's `stdin`, `stdout`, and
`stderr` in a fully non-blocking way.
non-blocking 방식으로 자식 프로세스의 `stdin`, `stdout`, 그리고 `stderr`를 통해
데이터 스트림이 가능하다.

To create a child process use `require('child_process').spawn()`.
자식 프로세스는 `require('child_process').spawn()` 를 이용해 생성한다.

Child processes always have three streams associated with them. `child.stdin`,
`child.stdout`, and `child.stderr`.
자식 프로세스는 3개의 스트림 `child.stdin`, `child.stdout` 그리고 `child.stderr`와
항상 연관되어 있다.

`ChildProcess` is an `EventEmitter`.
`ChildProcess` 는 `EventEmitter` 이다.

### Event:  'exit'

`function (code, signal) {}`

This event is emitted after the child process ends. If the process terminated
normally, `code` is the final exit code of the process, otherwise `null`. If
the process terminated due to receipt of a signal, `signal` is the string name
of the signal, otherwise `null`.
이 이벤트는 자식 프로세스가 종료된 후에 생성된다. 프로세스가 정상적으로 종료된 경우,
`code`는 프로세스 종료 코드이다. 아니면 null 이다. 프로세스가 시그널을 받고 종료하면
시그널은 그 `signal`의 문자열 이름이다. 그렇지 않으면 `null` 이다.

See `waitpid(2)`.
`waitpid(2)` 를 참조하세요.

### child.stdin

A `Writable Stream` that represents the child process's `stdin`.
Closing this stream via `end()` often causes the child process to terminate.
자식 프로세스의 `stdin`을 표현하는 `Writable Stream` 이다. 많은 경우, `end()`를 통해 이
스트림을 닫으면 자식 프로세스가 종료되는 원인이 된다.

### child.stdout

A `Readable Stream` that represents the child process's `stdout`.
자식 프로세스의 `stdout`를 표현하는 `Readable Stream` 이다.

### child.stderr

A `Readable Stream` that represents the child process's `stderr`.
자식 프로세스의 `stderr`를 표현하는 `Readable Stream` 이다.

### child.pid

The PID of the child process.
자식 프로세스의 PID.

Example:
예제 :

    var spawn = require('child_process').spawn,
        grep  = spawn('grep', ['ssh']);

    console.log('Spawned child pid: ' + grep.pid);
    grep.stdin.end();


### child_process.spawn(command, args=[], [options])

Launches a new process with the given `command`, with  command line arguments in `args`.
If omitted, `args` defaults to an empty Array.
명령줄 인자 `args`를 주어진 `command`로 새로운 프로세스를 시작한다. `args`를 생략하면 빈 배열이
기본이다.

The third argument is used to specify additional options, which defaults to:
세번째 인자는 추가 옵션을 지정하는데 사용되며, 그 기본값은 :

    { cwd: undefined,
      env: process.env,
      customFds: [-1, -1, -1],
      setsid: false
    }

`cwd` allows you to specify the working directory from which the process is spawned.
Use `env` to specify environment variables that will be visible to the new process.
With `customFds` it is possible to hook up the new process' [stdin, stdout, stderr] to
existing streams; `-1` means that a new stream should be created. `setsid`,
if set true, will cause the subprocess to be run in a new session.
`cwd`는 시작된 프로세스의 작업 디렉토리를 지정할 수 있다. `env`는 새로운 프로세스에 보이는 환경 변수를
지정하는데 사용한다. `customFds`는 새로운 프로세스의 [stdin, stout, stderr] 를 기존의 스트림에
연결하는 것을 허용한다. `-1`은 새로운 스트림이 만들어져야 한다는 걸 의미하는데 `setsid`를 true로
설정되면, 서브 프로세스는 새로운 세션에 실행됩니다.

Example of running `ls -lh /usr`, capturing `stdout`, `stderr`, and the exit code:
`ls -lh /usr` 실행하고 `stdout`, `stderr`, 종료 코드를 캡쳐하는 예제 :

    var util  = require('util'),
        spawn = require('child_process').spawn,
        ls    = spawn('ls', ['-lh', '/usr']);

    ls.stdout.on('data', function (data) {
      console.log('stdout: ' + data);
    });

    ls.stderr.on('data', function (data) {
      console.log('stderr: ' + data);
    });

    ls.on('exit', function (code) {
      console.log('child process exited with code ' + code);
    });


Example: A very elaborate way to run 'ps ax | grep ssh'
매우 정교한 방법으로 'ps ax | grep ssh' 를 실행 예제 :

    var util  = require('util'),
        spawn = require('child_process').spawn,
        ps    = spawn('ps', ['ax']),
        grep  = spawn('grep', ['ssh']);

    ps.stdout.on('data', function (data) {
      grep.stdin.write(data);
    });

    ps.stderr.on('data', function (data) {
      console.log('ps stderr: ' + data);
    });

    ps.on('exit', function (code) {
      if (code !== 0) {
        console.log('ps process exited with code ' + code);
      }
      grep.stdin.end();
    });

    grep.stdout.on('data', function (data) {
      console.log(data);
    });

    grep.stderr.on('data', function (data) {
      console.log('grep stderr: ' + data);
    });

    grep.on('exit', function (code) {
      if (code !== 0) {
        console.log('grep process exited with code ' + code);
      }
    });


Example of checking for failed exec:
exec 실패를 확인하는 예제:

    var spawn = require('child_process').spawn,
        child = spawn('bad_command');

    child.stderr.setEncoding('utf8');
    child.stderr.on('data', function (data) {
      if (/^execvp\(\)/.test(data)) {
        console.log('Failed to start child process.');
      }
    });


See also: `child_process.exec()`
`child_process.exec()` 도 참조하세요.

### child_process.exec(command, [options], callback)

High-level way to execute a command as a child process, buffer the
output, and return it all in a callback.
명령을 자식 프로세스로 실행하여 그 결과를 저장하고 콜백으로 전체를 전달하는
높은 수준의 방법이다.

    var util = require('util'),
        exec = require('child_process').exec,
        child;

    child = exec('cat *.js bad_file | wc -l',
      function (error, stdout, stderr) {
        console.log('stdout: ' + stdout);
        console.log('stderr: ' + stderr);
        if (error !== null) {
          console.log('exec error: ' + error);
        }
    });

The callback gets the arguments `(error, stdout, stderr)`. On success, `error`
will be `null`.  On error, `error` will be an instance of `Error` and `err.code`
will be the exit code of the child process, and `err.signal` will be set to the
signal that terminated the process.
콜백은 인자 `(error, stdout, stderr)`를 얻는다. 성공 시 `error`는 `null`일 것이다.
에러 시에는 `error`는 `Error`의 인스턴스이고, `err.code`는 자식 프로세스의 종료 코드 일것이다.
그리고 `err.signal`은 프로세스를 종료시키는 시그널이다.

There is a second optional argument to specify several options. The default options are
임의의 두번째 인자로 몇가지 옵션을 지정할 수 있다. 옵션의 기본값은:

    { encoding: 'utf8',
      timeout: 0,
      maxBuffer: 200*1024,
      killSignal: 'SIGTERM',
      cwd: null,
      env: null }

If `timeout` is greater than 0, then it will kill the child process
if it runs longer than `timeout` milliseconds. The child process is killed with
`killSignal` (default: `'SIGTERM'`). `maxBuffer` specifies the largest
amount of data allowed on stdout or stderr - if this value is exceeded then
the child process is killed.
`timeout`이 0보다 크면, 자식 프로세스 실행 시간이 `timeout` 밀리초보다 길어지면 kill 된다. 자식
프로세스는 `KillSignal` 으로 kill 된다.(기본값: `'SIGTERM'`). `maxBuffer` 는 stdout이나
stderr의 데이터 최대량을 정의한다. - 이 값을 초과하면 자식 프로세스는 kill 된다.


### child.kill(signal='SIGTERM')

Send a signal to the child process. If no argument is given, the process will
be sent `'SIGTERM'`. See `signal(7)` for a list of available signals.
자식 프로세스에 신호를 보낸다. 인수가 없다면, 자식프로세스는 `'SIGTERM'`를 받게 된다.
가능한 시그널 목록은 `signal(7)`을 참조하세요.

    var spawn = require('child_process').spawn,
        grep  = spawn('grep', ['ssh']);

    grep.on('exit', function (code, signal) {
      console.log('child process terminated due to receipt of signal '+signal);
    });

    // send SIGHUP to process
    grep.kill('SIGHUP');

Note that while the function is called `kill`, the signal delivered to the child
process may not actually kill it.  `kill` really just sends a signal to a process.
이 함수는 `kill`이라 불리지만, 실제로 전달되는 신호는 자식 프로세스를 죽이지 않는다.
`kill`은 단지 프로세스에 신호를 보낼뿐이다.

See `kill(2)`
`kill(2)`를 참조하세요.
