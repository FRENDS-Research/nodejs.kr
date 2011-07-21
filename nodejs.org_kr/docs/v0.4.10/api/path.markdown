## Path

This module contains utilities for dealing with file paths.  Use
`require('path')` to use it.  It provides the following methods:
이 모듈은 파일 경로를 취급하는 유용한 기능들을 포함한다.  사용하려면
`require('path')`를 호출해야 한다.  이 모듈은 다음의 메소드들을 제공한다.

### path.normalize(p)

Normalize a string path, taking care of `'..'` and `'.'` parts.
문자열 경로를 정규화 한다.  `'..'`와 `'.'` 요소를 주의해야 한다.

When multiple slashes are found, they're replaces by a single one;
when the path contains a trailing slash, it is preserved.
On windows backslashes are used.
다중 슬래시가 존재하면 하나로 치환한다.

Example:

    path.normalize('/foo/bar//baz/asdf/quux/..')
    // returns
    '/foo/bar/baz/asdf'

### path.join([path1], [path2], [...])

Join all arguments together and normalize the resulting path.
모든 인수를 하나로 묶고 그 결과로 경로를 결정한다.

Example:

    node> require('path').join(
    ...   '/foo', 'bar', 'baz/asdf', 'quux', '..')
    '/foo/bar/baz/asdf'

### path.resolve([from ...], to)

Resolves `to` to an absolute path.

If `to` isn't already absolute `from` arguments are prepended in right to left
order, until an absolute path is found. If after using all `from` paths still
no absolute path is found, the current working directory is used as well. The
resulting path is normalized, and trailing slashes are removed unless the path 
gets resolved to the root directory.

Another way to think of it is as a sequence of `cd` commands in a shell.

    path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile')

Is similar to:

    cd foo/bar
    cd /tmp/file/
    cd ..
    cd a/../subfile
    pwd

The difference is that the different paths don't need to exist and may also be
files.

Examples:

    path.resolve('/foo/bar', './baz')
    // returns
    '/foo/bar/baz'

    path.resolve('/foo/bar', '/tmp/file/')
    // returns
    '/tmp/file'

    path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif')
    // if currently in /home/myself/node, it returns
    '/home/myself/node/wwwroot/static_files/gif/image.gif'

### path.dirname(p)

Return the directory name of a path.  Similar to the Unix `dirname` command.
경로에 있는 디렉토리 이름을 반환한다. Unix의 `dirname` 명령과 유사하다.

Example:

    path.dirname('/foo/bar/baz/asdf/quux')
    // returns
    '/foo/bar/baz/asdf'

### path.basename(p, [ext])

Return the last portion of a path.  Similar to the Unix `basename` command.
경로의 마지막 요소를 반환한다.  Unix의 `basename` 명령과 유사하다.

Example:

    path.basename('/foo/bar/baz/asdf/quux.html')
    // returns
    'quux.html'

    path.basename('/foo/bar/baz/asdf/quux.html', '.html')
    // returns
    'quux'

### path.extname(p)

Return the extension of the path.  Everything after the last '.' in the last portion
of the path.  If there is no '.' in the last portion of the path or the only '.' is
the first character, then it returns an empty string.  Examples:
경로의 확장명을 반환한다.  경로의 마지막 요소에 마지막 '.' 뒤에 문자열이다.  마지막 요소에 '.'이
포함되지 않는 경우, 혹은 '.'이 첫 글자이면 빈 문자열을 반환한다. 예:

    path.extname('index.html')
    // returns
    '.html'

    path.extname('index')
    // returns
    ''

### path.exists(p, [callback])

Test whether or not the given path exists.  Then, call the `callback` argument
with either true or false. Example:
주어진 경로가 존재하는지 검사한다.  그리고 인수 `callback`을 true 혹은 false의 결과로 호출한다. 예:


    path.exists('/etc/passwd', function (exists) {
      util.debug(exists ? "it's there" : "no passwd!");
    });


### path.existsSync(p)

Synchronous version of `path.exists`.
`path.exists` 의 동기화 버젼
