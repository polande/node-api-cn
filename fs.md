# File System

    Stability: 3 - Stable

<!--name=fs-->

该模块是一个简单包装的标准 POSIX 文件操作函数集。
通过 `require('fs')` 调用。
模块所有的方法均有异步和同步的形式。

异步形式的方法需要一个完成时的回调函数作为最后一个参数。
虽然回调函数的参数由调用的方法所决定，
但通常第一个参数用于返回错误信息。
如果操作执行成功，则第一个参数为 `null` 或 `undefined`。

当使用同步形式的方法时，任何异常都会被立即抛出。
可以使用 try/catch 来处理异常或抛给上一层。

异步形式的例子：

    var fs = require('fs');

    fs.unlink('/tmp/hello', function (err) {
      if (err) throw err;
      console.log('successfully deleted /tmp/hello');
    });

同步形式的例子：

    var fs = require('fs');

    fs.unlinkSync('/tmp/hello')
    console.log('successfully deleted /tmp/hello');

异步方法不保证执行顺序。
所以下面的例子很容易出错：

    fs.rename('/tmp/hello', '/tmp/world', function (err) {
      if (err) throw err;
      console.log('renamed complete');
    });
    fs.stat('/tmp/world', function (err, stats) {
      if (err) throw err;
      console.log('stats: ' + JSON.stringify(stats));
    });

`fs.stat` 有可能在 `fs.rename` 之前执行。
正确的用法是把回调链起来。

    fs.rename('/tmp/hello', '/tmp/world', function (err) {
      if (err) throw err;
      fs.stat('/tmp/world', function (err, stats) {
        if (err) throw err;
        console.log('stats: ' + JSON.stringify(stats));
      });
    });

在繁重的任务中，强烈建议使用异步的方法。
同步的方法会阻塞整个进程直到完成处理，
也就是说会挂起所有的连接。

可以使用文件名的相对路径,
但是这个路径是相对于 `process.cwd()` 的。

大部分文件系统函数可以省略回调函数这个参数。
如果省略它，将会由默认的回调函数来重新抛出错误。
要获得原始调用点的堆栈跟踪信息，需要在环境变量里设置 NODE_DEBUG：

    $ cat script.js
    function bad() {
      require('fs').readFile('/');
    }
    bad();

    $ env NODE_DEBUG=fs node script.js
    fs.js:66
            throw err;
                  ^
    Error: EISDIR, read
        at rethrow (fs.js:61:21)
        at maybeCallback (fs.js:79:42)
        at Object.fs.readFile (fs.js:153:18)
        at bad (/path/to/script.js:2:17)
        at Object.<anonymous> (/path/to/script.js:5:1)
        <etc.>


## fs.rename(oldPath, newPath, callback)

异步的重命名文件。
回调函数只有一个参数：可能出现的异常信息。

## fs.renameSync(oldPath, newPath)

同步的重命名文件。

## fs.ftruncate(fd, len, callback)

Asynchronous ftruncate(2). No arguments other than a possible exception are
given to the completion callback.

## fs.ftruncateSync(fd, len)

Synchronous ftruncate(2).

## fs.truncate(path, len, callback)

异步的截断文件。
回调函数只有一个参数：可能出现的异常信息。

## fs.truncateSync(path, len)

同步的截断文件。

## fs.chown(path, uid, gid, callback)

Asynchronous chown(2). No arguments other than a possible exception are given
to the completion callback.

## fs.chownSync(path, uid, gid)

Synchronous chown(2).

## fs.fchown(fd, uid, gid, callback)

Asynchronous fchown(2). No arguments other than a possible exception are given
to the completion callback.

## fs.fchownSync(fd, uid, gid)

Synchronous fchown(2).

## fs.lchown(path, uid, gid, callback)

Asynchronous lchown(2). No arguments other than a possible exception are given
to the completion callback.

## fs.lchownSync(path, uid, gid)

Synchronous lchown(2).

## fs.chmod(path, mode, callback)

异步的修改文件权限。
回调函数只有一个参数：可能出现的异常信息。

## fs.chmodSync(path, mode)

同步的修改文件权限。

## fs.fchmod(fd, mode, callback)

Asynchronous fchmod(2). No arguments other than a possible exception
are given to the completion callback.

## fs.fchmodSync(fd, mode)

Synchronous fchmod(2).

## fs.lchmod(path, mode, callback)

Asynchronous lchmod(2). No arguments other than a possible exception
are given to the completion callback.

Only available on Mac OS X.

## fs.lchmodSync(path, mode)

Synchronous lchmod(2).

## fs.stat(path, callback)

异步的获取文件信息。
回调函数有两个参数 `(err, stats)`，
其中 `stats` 是一个 [fs.Stats](#fs_class_fs_stats) 对象。

## fs.lstat(path, callback)

Asynchronous lstat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object. `lstat()` is identical to `stat()`, except that if
`path` is a symbolic link, then the link itself is stat-ed, not the file that it
refers to.

## fs.fstat(fd, callback)

Asynchronous fstat(2). The callback gets two arguments `(err, stats)` where
`stats` is a `fs.Stats` object. `fstat()` is identical to `stat()`, except that
the file to be stat-ed is specified by the file descriptor `fd`.

## fs.statSync(path)

同步的获取文件信息。返回一个 `fs.Stats` 实例。

## fs.lstatSync(path)

Synchronous lstat(2). Returns an instance of `fs.Stats`.

## fs.fstatSync(fd)

Synchronous fstat(2). Returns an instance of `fs.Stats`.

## fs.link(srcpath, dstpath, callback)

异步的拷贝文件。
回调函数只有一个参数：可能出现的异常信息。

## fs.linkSync(srcpath, dstpath)

同步的拷贝文件。

## fs.symlink(srcpath, dstpath, [type], callback)

异步的创建快捷方式。
回调函数只有一个参数：可能出现的异常信息。
`type` 可以是 `'dir'`、`'file'`、或 `'junction'`（默认是 `'file'`），
且仅适用于 Windows 系统（其他系统平台会被忽略）。
注意：Windows 系统要求目标路径必须是一个绝对路径，
当使用 `'junction'` 时，`dstpath` 会被自动格式化为绝对路径。

## fs.symlinkSync(srcpath, dstpath, [type])

同步的创建快捷方式。

## fs.readlink(path, callback)

Asynchronous readlink(2). The callback gets two arguments `(err,
linkString)`.

## fs.readlinkSync(path)

Synchronous readlink(2). Returns the symbolic link's string value.

## fs.realpath(path, [cache], callback)

Asynchronous realpath(2). The `callback` gets two arguments `(err,
resolvedPath)`. May use `process.cwd` to resolve relative paths. `cache` is an
object literal of mapped paths that can be used to force a specific path
resolution or avoid additional `fs.stat` calls for known real paths.

Example:

    var cache = {'/etc':'/private/etc'};
    fs.realpath('/etc/passwd', cache, function (err, resolvedPath) {
      if (err) throw err;
      console.log(resolvedPath);
    });

## fs.realpathSync(path, [cache])

Synchronous realpath(2). Returns the resolved path.

## fs.unlink(path, callback)

异步的删除文件。
回调函数只有一个参数：可能出现的异常信息。

## fs.unlinkSync(path)

同步的删除文件。

## fs.rmdir(path, callback)

异步的删除目录。
回调函数只有一个参数：可能出现的异常信息。

## fs.rmdirSync(path)

同步的删除目录。

## fs.mkdir(path, [mode], callback)

异步的创建目录。
回调函数只有一个参数：可能出现的异常信息。`mode` 默认为 `0777`。

## fs.mkdirSync(path, [mode])

同步的创建目录。

## fs.readdir(path, callback)

异步的读取目录内容。
回调函数有两个参数 `(err, files)`，
其中 `files` 是目录所含文件的文件名数组，排除 `'.'` 和 `'..'`。

## fs.readdirSync(path)

同步的读取目录内容。
返回文件名数组，排除 `'.'` 和 `'..'`。

## fs.close(fd, callback)

Asynchronous close(2).  No arguments other than a possible exception are given
to the completion callback.

## fs.closeSync(fd)

Synchronous close(2).

## fs.open(path, flags, [mode], callback)

Asynchronous file open. See open(2). `flags` can be:

* `'r'` - Open file for reading.
An exception occurs if the file does not exist.

* `'r+'` - Open file for reading and writing.
An exception occurs if the file does not exist.

* `'rs'` - Open file for reading in synchronous mode. Instructs the operating
  system to bypass the local file system cache.

  This is primarily useful for opening files on NFS mounts as it allows you to
  skip the potentially stale local cache. It has a very real impact on I/O
  performance so don't use this flag unless you need it.

  Note that this doesn't turn `fs.open()` into a synchronous blocking call.
  If that's what you want then you should be using `fs.openSync()`

* `'rs+'` - Open file for reading and writing, telling the OS to open it
  synchronously. See notes for `'rs'` about using this with caution.

* `'w'` - Open file for writing.
The file is created (if it does not exist) or truncated (if it exists).

* `'wx'` - Like `'w'` but fails if `path` exists.

* `'w+'` - Open file for reading and writing.
The file is created (if it does not exist) or truncated (if it exists).

* `'wx+'` - Like `'w+'` but fails if `path` exists.

* `'a'` - Open file for appending.
The file is created if it does not exist.

* `'ax'` - Like `'a'` but fails if `path` exists.

* `'a+'` - Open file for reading and appending.
The file is created if it does not exist.

* `'ax+'` - Like `'a+'` but fails if `path` exists.

`mode` sets the file mode (permission and sticky bits), but only if the file was
created. It defaults to `0666`, readable and writeable.

The callback gets two arguments `(err, fd)`.

The exclusive flag `'x'` (`O_EXCL` flag in open(2)) ensures that `path` is newly
created. On POSIX systems, `path` is considered to exist even if it is a symlink
to a non-existent file. The exclusive flag may or may not work with network file
systems.

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

## fs.openSync(path, flags, [mode])

Synchronous version of `fs.open()`.

## fs.utimes(path, atime, mtime, callback)
## fs.utimesSync(path, atime, mtime)

Change file timestamps of the file referenced by the supplied path.

## fs.futimes(fd, atime, mtime, callback)
## fs.futimesSync(fd, atime, mtime)

Change the file timestamps of a file referenced by the supplied file
descriptor.

## fs.fsync(fd, callback)

Asynchronous fsync(2). No arguments other than a possible exception are given
to the completion callback.

## fs.fsyncSync(fd)

Synchronous fsync(2).

## fs.write(fd, buffer, offset, length[, position], callback)

Write `buffer` to the file specified by `fd`.

`offset` and `length` determine the part of the buffer to be written.

`position` refers to the offset from the beginning of the file where this data
should be written. If `typeof position !== 'number'`, the data will be written
at the current position. See pwrite(2).

The callback will be given three arguments `(err, written, buffer)` where
`written` specifies how many _bytes_ were written from `buffer`.

Note that it is unsafe to use `fs.write` multiple times on the same file
without waiting for the callback. For this scenario,
`fs.createWriteStream` is strongly recommended.

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

## fs.write(fd, data[, position[, encoding]], callback)

Write `data` to the file specified by `fd`.  If `data` is not a Buffer instance
then the value will be coerced to a string.

`position` refers to the offset from the beginning of the file where this data
should be written. If `typeof position !== 'number'` the data will be written at
the current position. See pwrite(2).

`encoding` is the expected string encoding.

The callback will receive the arguments `(err, written, string)` where `written`
specifies how many _bytes_ the passed string required to be written. Note that
bytes written is not the same as string characters. See
[Buffer.byteLength](buffer.html#buffer_class_method_buffer_bytelength_string_encoding).

Unlike when writing `buffer`, the entire string must be written. No substring
may be specified. This is because the byte offset of the resulting data may not
be the same as the string offset.

Note that it is unsafe to use `fs.write` multiple times on the same file
without waiting for the callback. For this scenario,
`fs.createWriteStream` is strongly recommended.

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

## fs.writeSync(fd, buffer, offset, length[, position])

## fs.writeSync(fd, data[, position[, encoding]])

Synchronous versions of `fs.write()`. Returns the number of bytes written.

## fs.read(fd, buffer, offset, length, position, callback)

Read data from the file specified by `fd`.

`buffer` is the buffer that the data will be written to.

`offset` is the offset in the buffer to start writing at.

`length` is an integer specifying the number of bytes to read.

`position` is an integer specifying where to begin reading from in the file.
If `position` is `null`, data will be read from the current file position.

The callback is given the three arguments, `(err, bytesRead, buffer)`.

## fs.readSync(fd, buffer, offset, length, position)

Synchronous version of `fs.read`. Returns the number of `bytesRead`.

## fs.readFile(filename, [options], callback)

* `filename` {String}
* `options` {Object}
  * `encoding` {String | Null} default = `null`
  * `flag` {String} default = `'r'`
* `callback` {Function}

异步的读取文件的全部内容。例子：

    fs.readFile('/etc/passwd', function (err, data) {
      if (err) throw err;
      console.log(data);
    });

回调函数有两个参数 `(err, data)`，
其中 `data` 是文件的内容。

如果没有指定编码方式，则返回原始的 buffer。


## fs.readFileSync(filename, [options])

同步的读取文件。返回 `filename` 的内容。

如果指定了 `encoding`，则返回一个字符串，
否则返回一个 buffer。


## fs.writeFile(filename, data, [options], callback)

* `filename` {String}
* `data` {String | Buffer}
* `options` {Object}
  * `encoding` {String | Null} default = `'utf8'`
  * `mode` {Number} default = `438` (aka `0666` in Octal)
  * `flag` {String} default = `'w'`
* `callback` {Function}

异步的将数据写入一个文件。如果文件存在，则会被替换。
`data` 可以是字符串或 buffer。

如果 `data` 是一个 buffer，则 `encoding` 会被忽视。
`encoding` 默认是 `'utf8'`。

例子：

    fs.writeFile('message.txt', 'Hello Node', function (err) {
      if (err) throw err;
      console.log('It\'s saved!');
    });

## fs.writeFileSync(filename, data, [options])

同步的写入文件。

## fs.appendFile(filename, data, [options], callback)

* `filename` {String}
* `data` {String | Buffer}
* `options` {Object}
  * `encoding` {String | Null} default = `'utf8'`
  * `mode` {Number} default = `438` (aka `0666` in Octal)
  * `flag` {String} default = `'a'`
* `callback` {Function}

异步的添加数据到文件末尾。如果文件不存在，则创建一个新的文件。
`data` 可以是字符串或 buffer。

例子：

    fs.appendFile('message.txt', 'data to append', function (err) {
      if (err) throw err;
      console.log('The "data to append" was appended to file!');
    });

## fs.appendFileSync(filename, data, [options])

同步的添加数据到文件末尾。

## fs.watchFile(filename, [options], listener)

    Stability: 2 - Unstable.  Use fs.watch instead, if possible.

Watch for changes on `filename`. The callback `listener` will be called each
time the file is accessed.

The second argument is optional. The `options` if provided should be an object
containing two members a boolean, `persistent`, and `interval`. `persistent`
indicates whether the process should continue to run as long as files are
being watched. `interval` indicates how often the target should be polled,
in milliseconds. The default is `{ persistent: true, interval: 5007 }`.

The `listener` gets two arguments the current stat object and the previous
stat object:

    fs.watchFile('message.text', function (curr, prev) {
      console.log('the current mtime is: ' + curr.mtime);
      console.log('the previous mtime was: ' + prev.mtime);
    });

These stat objects are instances of `fs.Stat`.

If you want to be notified when the file was modified, not just accessed
you need to compare `curr.mtime` and `prev.mtime`.

## fs.unwatchFile(filename, [listener])

    Stability: 2 - Unstable.  Use fs.watch instead, if possible.

Stop watching for changes on `filename`. If `listener` is specified, only that
particular listener is removed. Otherwise, *all* listeners are removed and you
have effectively stopped watching `filename`.

Calling `fs.unwatchFile()` with a filename that is not being watched is a
no-op, not an error.

## fs.watch(filename, [options], [listener])

    Stability: 2 - Unstable.

Watch for changes on `filename`, where `filename` is either a file or a
directory.  The returned object is a [fs.FSWatcher](#fs_class_fs_fswatcher).

The second argument is optional. The `options` if provided should be an object.
The supported boolean members are `persistent` and `recursive`. `persistent`
indicates whether the process should continue to run as long as files are being
watched. `recursive` indicates whether all subdirectories should be watched, or
only the current directory. This applies when a directory is specified, and only
on supported platforms (See Caveats below).

The default is `{ persistent: true, recursive: false }`.

The listener callback gets two arguments `(event, filename)`.  `event` is either
'rename' or 'change', and `filename` is the name of the file which triggered
the event.

### Caveats

<!--type=misc-->

The `fs.watch` API is not 100% consistent across platforms, and is
unavailable in some situations.

The recursive option is currently supported on OS X. Only FSEvents supports this
type of file watching so it is unlikely any additional platforms will be added
soon.

#### Availability

<!--type=misc-->

This feature depends on the underlying operating system providing a way
to be notified of filesystem changes.

* On Linux systems, this uses `inotify`.
* On BSD systems, this uses `kqueue`.
* On OS X, this uses `kqueue` for files and 'FSEvents' for directories.
* On SunOS systems (including Solaris and SmartOS), this uses `event ports`.
* On Windows systems, this feature depends on `ReadDirectoryChangesW`.

If the underlying functionality is not available for some reason, then
`fs.watch` will not be able to function.  For example, watching files or
directories on network file systems (NFS, SMB, etc.) often doesn't work
reliably or at all.

You can still use `fs.watchFile`, which uses stat polling, but it is slower and
less reliable.

#### Filename Argument

<!--type=misc-->

Providing `filename` argument in the callback is not supported
on every platform (currently it's only supported on Linux and Windows).  Even
on supported platforms `filename` is not always guaranteed to be provided.
Therefore, don't assume that `filename` argument is always provided in the
callback, and have some fallback logic if it is null.

    fs.watch('somedir', function (event, filename) {
      console.log('event is: ' + event);
      if (filename) {
        console.log('filename provided: ' + filename);
      } else {
        console.log('filename not provided');
      }
    });

## fs.exists(path, callback)

Test whether or not the given path exists by checking with the file system.
Then call the `callback` argument with either true or false.  Example:

    fs.exists('/etc/passwd', function (exists) {
      util.debug(exists ? "it's there" : "no passwd!");
    });

`fs.exists()` is an anachronism and exists only for historical reasons.
There should almost never be a reason to use it in your own code.

In particular, checking if a file exists before opening it is an anti-pattern
that leaves you vulnerable to race conditions: another process may remove the
file between the calls to `fs.exists()` and `fs.open()`.  Just open the file
and handle the error when it's not there.

## fs.existsSync(path)

Synchronous version of `fs.exists`.

## Class: fs.Stats

Objects returned from `fs.stat()`, `fs.lstat()` and `fs.fstat()` and their
synchronous counterparts are of this type.

 - `stats.isFile()`
 - `stats.isDirectory()`
 - `stats.isBlockDevice()`
 - `stats.isCharacterDevice()`
 - `stats.isSymbolicLink()` (only valid with  `fs.lstat()`)
 - `stats.isFIFO()`
 - `stats.isSocket()`

For a regular file `util.inspect(stats)` would return a string very
similar to this:

    { dev: 2114,
      ino: 48064969,
      mode: 33188,
      nlink: 1,
      uid: 85,
      gid: 100,
      rdev: 0,
      size: 527,
      blksize: 4096,
      blocks: 8,
      atime: Mon, 10 Oct 2011 23:24:11 GMT,
      mtime: Mon, 10 Oct 2011 23:24:11 GMT,
      ctime: Mon, 10 Oct 2011 23:24:11 GMT,
      birthtime: Mon, 10 Oct 2011 23:24:11 GMT }

Please note that `atime`, `mtime`, `birthtime`, and `ctime` are
instances of [Date][MDN-Date] object and to compare the values of
these objects you should use appropriate methods. For most general
uses [getTime()][MDN-Date-getTime] will return the number of
milliseconds elapsed since _1 January 1970 00:00:00 UTC_ and this
integer should be sufficient for any comparison, however there are
additional methods which can be used for displaying fuzzy information.
More details can be found in the [MDN JavaScript Reference][MDN-Date]
page.

[MDN-Date]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date
[MDN-Date-getTime]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date/getTime

### Stat Time Values

The times in the stat object have the following semantics:

* `atime` "Access Time" - Time when file data last accessed.  Changed
  by the `mknod(2)`, `utimes(2)`, and `read(2)` system calls.
* `mtime` "Modified Time" - Time when file data last modified.
  Changed by the `mknod(2)`, `utimes(2)`, and `write(2)` system calls.
* `ctime` "Change Time" - Time when file status was last changed
  (inode data modification).  Changed by the `chmod(2)`, `chown(2)`,
  `link(2)`, `mknod(2)`, `rename(2)`, `unlink(2)`, `utimes(2)`,
  `read(2)`, and `write(2)` system calls.
* `birthtime` "Birth Time" -  Time of file creation. Set once when the
  file is created.  On filesystems where birthtime is not available,
  this field may instead hold either the `ctime` or
  `1970-01-01T00:00Z` (ie, unix epoch timestamp `0`).  On Darwin and
  other FreeBSD variants, also set if the `atime` is explicitly set to
  an earlier value than the current `birthtime` using the `utimes(2)`
  system call.

Prior to Node v0.12, the `ctime` held the `birthtime` on Windows
systems.  Note that as of v0.12, `ctime` is not "creation time", and
on Unix systems, it never was.

## fs.createReadStream(path, [options])

Returns a new ReadStream object (See `Readable Stream`).

`options` is an object with the following defaults:

    { flags: 'r',
      encoding: null,
      fd: null,
      mode: 0666,
      autoClose: true
    }

`options` can include `start` and `end` values to read a range of bytes from
the file instead of the entire file.  Both `start` and `end` are inclusive and
start at 0. The `encoding` can be `'utf8'`, `'ascii'`, or `'base64'`.

If `autoClose` is false, then the file descriptor won't be closed, even if
there's an error.  It is your responsibility to close it and make sure
there's no file descriptor leak.  If `autoClose` is set to true (default
behavior), on `error` or `end` the file descriptor will be closed
automatically.

An example to read the last 10 bytes of a file which is 100 bytes long:

    fs.createReadStream('sample.txt', {start: 90, end: 99});


## Class: fs.ReadStream

`ReadStream` is a [Readable Stream](stream.html#stream_class_stream_readable).

### Event: 'open'

* `fd` {Integer} file descriptor used by the ReadStream.

Emitted when the ReadStream's file is opened.


## fs.createWriteStream(path, [options])

Returns a new WriteStream object (See `Writable Stream`).

`options` is an object with the following defaults:

    { flags: 'w',
      encoding: null,
      mode: 0666 }

`options` may also include a `start` option to allow writing data at
some position past the beginning of the file.  Modifying a file rather
than replacing it may require a `flags` mode of `r+` rather than the
default mode `w`.

## Class: fs.WriteStream

`WriteStream` is a [Writable Stream](stream.html#stream_class_stream_writable).

### Event: 'open'

* `fd` {Integer} file descriptor used by the WriteStream.

Emitted when the WriteStream's file is opened.

### file.bytesWritten

The number of bytes written so far. Does not include data that is still queued
for writing.

## Class: fs.FSWatcher

Objects returned from `fs.watch()` are of this type.

### watcher.close()

Stop watching for changes on the given `fs.FSWatcher`.

### Event: 'change'

* `event` {String} The type of fs change
* `filename` {String} The filename that changed (if relevant/available)

Emitted when something changes in a watched directory or file.
See more details in [fs.watch](#fs_fs_watch_filename_options_listener).

### Event: 'error'

* `error` {Error object}

Emitted when an error occurs.
