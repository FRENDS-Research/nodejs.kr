## File System

File I/O is provided by simple wrappers around standard POSIX functions.  To
use this module do `require('fs')`. All the methods have asynchronous and
synchronous forms.
File I/O는 POSIX 표준 함수에 대한 간단한 래퍼로 제공된다. 이 모듈을 사용하려면
`require('fs')`를 한다. 모든 메소드는 비동기와 동기의 형식이 있다.

The asynchronous form always take a completion callback as its last argument.
The arguments passed to the completion callback depend on the method, but the
first argument is always reserved for an exception. If the operation was
completed successfully, then the first argument will be `null` or `undefined`.
비동기 형식은 항상 마지막 인자로 완료 콜백을 받는다. 인수로 전달된 완료 콜백 메서드에
의존하지만, 최초의 인수는 항상 예외를 위해 예약되어 있다. 작업이 성공적으로 완료되면
첫 번째 인수는 `null` 또는 `undefined` 이다.

Here is an example of the asynchronous version:
비동기 버전의 예 :

    var fs = require('fs');

    fs.unlink('/tmp/hello', function (err) {
      if (err) throw err;
      console.log('successfully deleted /tmp/hello');
    });

Here is the synchronous version:
동기화 버전의 예 :

    var fs = require('fs');

    fs.unlinkSync('/tmp/hello')
    console.log('successfully deleted /tmp/hello');

With the asynchronous methods there is no guaranteed ordering. So the
following is prone to error:
비동기 메소드는 순서를 보장하지 않는다. 그래서 다음과 같은 오류가 나기 쉽다.

    fs.rename('/tmp/hello', '/tmp/world', function (err) {
      if (err) throw err;
      console.log('renamed complete');
    });
    fs.stat('/tmp/world', function (err, stats) {
      if (err) throw err;
      console.log('stats: ' + JSON.stringify(stats));
    });

It could be that `fs.stat` is executed before `fs.rename`.
The correct way to do this is to chain the callbacks.
`fs.stat`는 `fs.rename`보다 먼저 실행되는 수도 있다. 정확한 방법은
콜백을 연결하는 것이다.

    fs.rename('/tmp/hello', '/tmp/world', function (err) {
      if (err) throw err;
      fs.stat('/tmp/world', function (err, stats) {
        if (err) throw err;
        console.log('stats: ' + JSON.stringify(stats));
      });
    });

In busy processes, the programmer is _strongly encouraged_ to use the
asynchronous versions of these calls. The synchronous versions will block
the entire process until they complete--halting all connections.
처리할게 많은 프로세스에서는 프로그래머가 이런 비동기 버전의 호출을 사용하기를 강력히 권한다.
동기화 버전은 그들이 완료될 때까지 프로세스를 차단한다. 모든 연결을 중단한다.

Relative path to filename can be used, remember however that this path will be relative
to `process.cwd()`.
파일 이름은 상대 경로를 사용할 수 있다. 그러나 이 경로는 `process.cwd()` 에서 상대적임을 기억하세요.

### fs.rename(path1, path2, [callback])

Asynchronous rename(2). No arguments other than a possible exception are given
to the completion callback.
비동기 방식 ftruncate(2). 완료 콜백에는 발생가능 한 예외 이외에는 인수가 전달되지 않는다.

### fs.renameSync(path1, path2)

Synchronous rename(2).
동기 방식 rename(2).

### fs.truncate(fd, len, [callback])

Asynchronous ftruncate(2). No arguments other than a possible exception are
given to the completion callback.
비동기 방식 ftruncate(2). 완료 콜백에는 발생가능 한 예외 이외에는 인수가 전달되지 않는다.

### fs.truncateSync(fd, len)

Synchronous ftruncate(2).
동기 방식 ftruncate(2).

### fs.chown(path, uid, gid, [callback])

Asycnronous chown(2). No arguments other than a possible exception are given
to the completion callback.
비동기 방식 chown(2). 완료 콜백에는 발생가능한 예외 이외에는 인수가 전달되지 않는다.

### fs.chownSync(path, uid, gid)

Synchronous chown(2).
동기 방식 chown(2).

### fs.fchown(path, uid, gid, [callback])

Asycnronous fchown(2). No arguments other than a possible exception are given
to the completion callback.
비동기 방식 fchown(2). 완료 콜백에는 발생가능한 예외 이외에는 인수가 전달되지 않는다.

### fs.fchownSync(path, uid, gid)

Synchronous fchown(2).
동기 방식 fchown(2).

### fs.lchown(path, uid, gid, [callback])

Asycnronous lchown(2). No arguments other than a possible exception are given
to the completion callback.
비동기 방식 lchown(2). 완료 콜백에는 발생가능한 예외 이외에는 인수가 전달되지 않는다.

### fs.lchownSync(path, uid, gid)

Synchronous lchown(2).
동기 방식 lchown(2).

### fs.chmod(path, mode, [callback])

Asynchronous chmod(2). No arguments other than a possible exception are given
to the completion callback.
비동기 방식 chmod(2). 완료 콜백에는 발생가능한 예외 이외에는 인수가 전달되지 않는다.

### fs.chmodSync(path, mode)

Synchronous chmod(2).
동기 방식 chmod(2).

### fs.fchmod(fd, mode, [callback])

Asynchronous fchmod(2). No arguments other than a possible exception
are given to the completion callback.
비동기 방식 fchmod(2). 완료 콜백에는 발생가능한 예외 이외에는 인수가 전달되지 않는다.

### fs.fchmodSync(path, mode)

Synchronous fchmod(2).
동기 방식 fchmod(2).

### fs.lchmod(fd, mode, [callback])

Asynchronous lchmod(2). No arguments other than a possible exception
are given to the completion callback.
비동기 방식 lchmod(2). 완료 콜백에는 발생가능한 예외 이외에는 인수가 전달되지 않는다.

### fs.lchmodSync(path, mode)

Synchronous lchmod(2).
동기 방식 lchmod(2).

### fs.stat(path, [callback])

Asynchronous stat(2). The callback gets two arguments `(err, stats)` where
`stats` is a [`fs.Stats`](#fs.Stats) object. It looks like this:
비동기 stat (2). 콜백은 2 개의 인수`(err, stats)`를 받는다. `stats`는 `fs.Stats` 개체이다. 다음과 같다.

    { dev: 2049,
      ino: 305352,
      mode: 16877,
      nlink: 12,
      uid: 1000,
      gid: 1000,
      rdev: 0,
      size: 4096,
      blksize: 4096,
      blocks: 8,
      atime: '2009-06-29T11:11:55Z',
      mtime: '2009-06-29T11:11:40Z',
      ctime: '2009-06-29T11:11:40Z' }

See the [fs.Stats](#fs.Stats) section below for more information.
더 자세한 사항은 아래의 `fs.Stats` 절을 참조하세요.

### fs.lstat(path, [callback])

Asynchronous lstat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object. `lstat()` is identical to `stat()`, except that if
`path` is a symbolic link, then the link itself is stat-ed, not the file that it
refers to.

### fs.fstat(fd, [callback])

Asynchronous fstat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object. `fstat()` is identical to `stat()`, except that
the file to be stat-ed is specified by the file descriptor `fd`.

### fs.statSync(path)

Synchronous stat(2). Returns an instance of `fs.Stats`.

### fs.lstatSync(path)

Synchronous lstat(2). Returns an instance of `fs.Stats`.

### fs.fstatSync(fd)

Synchronous fstat(2). Returns an instance of `fs.Stats`.

### fs.link(srcpath, dstpath, [callback])

Asynchronous link(2). No arguments other than a possible exception are given to
the completion callback.

### fs.linkSync(srcpath, dstpath)

Synchronous link(2).

### fs.symlink(linkdata, path, [callback])

Asynchronous symlink(2). No arguments other than a possible exception are given
to the completion callback.

### fs.symlinkSync(linkdata, path)

Synchronous symlink(2).

### fs.readlink(path, [callback])

Asynchronous readlink(2). The callback gets two arguments `(err,
resolvedPath)`.

### fs.readlinkSync(path)

Synchronous readlink(2). Returns the resolved path.

### fs.realpath(path, [callback])

Asynchronous realpath(2).  The callback gets two arguments `(err,
resolvedPath)`.

### fs.realpathSync(path)

Synchronous realpath(2). Returns the resolved path.

### fs.unlink(path, [callback])

Asynchronous unlink(2). No arguments other than a possible exception are given
to the completion callback.

### fs.unlinkSync(path)

Synchronous unlink(2).

### fs.rmdir(path, [callback])

Asynchronous rmdir(2). No arguments other than a possible exception are given
to the completion callback.

### fs.rmdirSync(path)

Synchronous rmdir(2).

### fs.mkdir(path, mode, [callback])

Asynchronous mkdir(2). No arguments other than a possible exception are given
to the completion callback.

### fs.mkdirSync(path, mode)

Synchronous mkdir(2).

### fs.readdir(path, [callback])

Asynchronous readdir(3).  Reads the contents of a directory.
The callback gets two arguments `(err, files)` where `files` is an array of
the names of the files in the directory excluding `'.'` and `'..'`.

### fs.readdirSync(path)

Synchronous readdir(3). Returns an array of filenames excluding `'.'` and
`'..'`.

### fs.close(fd, [callback])

Asynchronous close(2).  No arguments other than a possible exception are given
to the completion callback.

### fs.closeSync(fd)

Synchronous close(2).

### fs.open(path, flags, [mode], [callback])

Asynchronous file open. See open(2). `flags` can be:

* `'r'` - Open file for reading.
An exception occurs if the file does not exist.

* `'r+'` - Open file for reading and writing. 
An exception occurs if the file does not exist.

* `'w'` - Open file for writing.
The file is created (if it does not exist) or truncated (if it exists).

* `'w+'` - Open file for reading and writing.
The file is created (if it does not exist) or truncated (if it exists).

* `'a'` - Open file for appending.
The file is created if it does not exist.

* `'a+'` - Open file for reading and appending.
The file is created if it does not exist.

`mode` defaults to `0666`. The callback gets two arguments `(err, fd)`.

### fs.openSync(path, flags, [mode])

Synchronous open(2).

### fs.write(fd, buffer, offset, length, position, [callback])

Write `buffer` to the file specified by `fd`.

`offset` and `length` determine the part of the buffer to be written.

`position` refers to the offset from the beginning of the file where this data
should be written. If `position` is `null`, the data will be written at the
current position.
See pwrite(2).

The callback will be given three arguments `(err, written, buffer)` where `written`
specifies how many _bytes_ were written into `buffer`.

Note that it is unsafe to use `fs.write` multiple times on the same file
without waiting for the callback. For this scenario,
`fs.createWriteStream` is strongly recommended.

### fs.writeSync(fd, buffer, offset, length, position)

Synchronous version of buffer-based `fs.write()`. Returns the number of bytes
written.

### fs.writeSync(fd, str, position, encoding='utf8')

Synchronous version of string-based `fs.write()`. Returns the number of bytes
written.

### fs.read(fd, buffer, offset, length, position, [callback])

Read data from the file specified by `fd`.

`buffer` is the buffer that the data will be written to.

`offset` is offset within the buffer where writing will start.

`length` is an integer specifying the number of bytes to read.

`position` is an integer specifying where to begin reading from in the file.
If `position` is `null`, data will be read from the current file position.

The callback is given the three arguments, `(err, bytesRead, buffer)`.

### fs.readSync(fd, buffer, offset, length, position)

Synchronous version of buffer-based `fs.read`. Returns the number of
`bytesRead`.

### fs.readSync(fd, length, position, encoding)

Synchronous version of string-based `fs.read`. Returns the number of
`bytesRead`.

### fs.readFile(filename, [encoding], [callback])

Asynchronously reads the entire contents of a file. Example:

    fs.readFile('/etc/passwd', function (err, data) {
      if (err) throw err;
      console.log(data);
    });

The callback is passed two arguments `(err, data)`, where `data` is the
contents of the file.

If no encoding is specified, then the raw buffer is returned.


### fs.readFileSync(filename, [encoding])

Synchronous version of `fs.readFile`. Returns the contents of the `filename`.

If `encoding` is specified then this function returns a string. Otherwise it
returns a buffer.


### fs.writeFile(filename, data, encoding='utf8', [callback])

Asynchronously writes data to a file, replacing the file if it already exists.
`data` can be a string or a buffer.

Example:

    fs.writeFile('message.txt', 'Hello Node', function (err) {
      if (err) throw err;
      console.log('It\'s saved!');
    });

### fs.writeFileSync(filename, data, encoding='utf8')

The synchronous version of `fs.writeFile`.

### fs.watchFile(filename, [options], listener)

Watch for changes on `filename`. The callback `listener` will be called each
time the file is accessed.

The second argument is optional. The `options` if provided should be an object
containing two members a boolean, `persistent`, and `interval`, a polling
value in milliseconds. The default is `{ persistent: true, interval: 0 }`.

The `listener` gets two arguments the current stat object and the previous
stat object:

    fs.watchFile('message.text', function (curr, prev) {
      console.log('the current mtime is: ' + curr.mtime);
      console.log('the previous mtime was: ' + prev.mtime);
    });

These stat objects are instances of `fs.Stat`.

If you want to be notified when the file was modified, not just accessed
you need to compare `curr.mtime` and `prev.mtime`.


### fs.unwatchFile(filename)

Stop watching for changes on `filename`.

## fs.Stats

Objects returned from `fs.stat()` and `fs.lstat()` are of this type.

 - `stats.isFile()`
 - `stats.isDirectory()`
 - `stats.isBlockDevice()`
 - `stats.isCharacterDevice()`
 - `stats.isSymbolicLink()` (only valid with  `fs.lstat()`)
 - `stats.isFIFO()`
 - `stats.isSocket()`


## fs.ReadStream

`ReadStream` is a `Readable Stream`.

### Event: 'open'

`function (fd) { }`

 `fd` is the file descriptor used by the ReadStream.

### fs.createReadStream(path, [options])

Returns a new ReadStream object (See `Readable Stream`).

`options` is an object with the following defaults:

    { flags: 'r',
      encoding: null,
      fd: null,
      mode: 0666,
      bufferSize: 64 * 1024
    }

`options` can include `start` and `end` values to read a range of bytes from
the file instead of the entire file.  Both `start` and `end` are inclusive and
start at 0.

An example to read the last 10 bytes of a file which is 100 bytes long:

    fs.createReadStream('sample.txt', {start: 90, end: 99});


## fs.WriteStream

`WriteStream` is a `Writable Stream`.

### Event: 'open'

`function (fd) { }`

 `fd` is the file descriptor used by the WriteStream.

### fs.createWriteStream(path, [options])

Returns a new WriteStream object (See `Writable Stream`).

`options` is an object with the following defaults:

    { flags: 'w',
      encoding: null,
      mode: 0666 }
