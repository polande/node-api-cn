# Global Objects

<!-- type=misc -->

这些对象在所有模块中均可使用。
有些对象实际上并非在全局作用域内，而是在模块作用域内。

## global

<!-- type=global -->

* {Object} The global namespace object.

在浏览器中，顶级作用域就是全局作用域。
也就是说在浏览器中，如果在全局作用域内 `var something` 将会声明一个全局变量。
在 Node 中则不同。
顶级作用域并非全局作用域，在 Node 模块里的 `var something` 只属于当前模块。

## process

<!-- type=global -->

* {Object}

进程对象。参阅 [process 对象](process.html#process_process) 章节。

## console

<!-- type=global -->

* {Object}

Used to print to stdout and stderr. See the [console][] section.

## Class: Buffer

<!-- type=global -->

* {Function}

用于处理二进制数据。参阅 [buffer 章节](buffer.html)。

## require()

<!-- type=var -->

* {Function}

用于引入模块。参阅 [Modules](modules.html) 章节。
`require` 实际上并非全局的，而是各个模块本地的。

### require.resolve()

Use the internal `require()` machinery to look up the location of a module,
but rather than loading the module, just return the resolved filename.

### require.cache

* {Object}

Modules are cached in this object when they are required. By deleting a key
value from this object, the next `require` will reload the module.

### require.extensions

    Stability: 0 - Deprecated

* {Object}

Instruct `require` on how to handle certain file extensions.

Process files with the extension `.sjs` as `.js`:

    require.extensions['.sjs'] = require.extensions['.js'];

**Deprecated**  In the past, this list has been used to load
non-JavaScript modules into Node by compiling them on-demand.
However, in practice, there are much better ways to do this, such as
loading modules via some other Node program, or compiling them to
JavaScript ahead of time.

Since the Module system is locked, this feature will probably never go
away.  However, it may have subtle bugs and complexities that are best
left untouched.

## __filename

<!-- type=var -->

* {String}

The filename of the code being executed.  This is the resolved absolute path
of this code file.  For a main program this is not necessarily the same
filename used in the command line.  The value inside a module is the path
to that module file.

Example: running `node example.js` from `/Users/mjr`

    console.log(__filename);
    // /Users/mjr/example.js

`__filename` isn't actually a global but rather local to each module.

## __dirname

<!-- type=var -->

* {String}

The name of the directory that the currently executing script resides in.

Example: running `node example.js` from `/Users/mjr`

    console.log(__dirname);
    // /Users/mjr

`__dirname` isn't actually a global but rather local to each module.


## module

<!-- type=var -->

* {Object}

A reference to the current module. In particular
`module.exports` is used for defining what a module exports and makes
available through `require()`.

`module` isn't actually a global but rather local to each module.

See the [module system documentation][] for more information.

## exports

<!-- type=var -->

`module.exports` 的引用。
何时使用 `exports` 与 `module.exports`，
详见 [module 系统文档](modules.html)。

`exports` 实际上并非全局的，而是各个模块本地的。

详见 [module 系统文档](modules.html)。

详见 [module 章节](modules.html)。

## setTimeout(cb, ms)

Run callback `cb` after *at least* `ms` milliseconds. The actual delay depends
on external factors like OS timer granularity and system load.

The timeout must be in the range of 1-2,147,483,647 inclusive. If the value is
outside that range, it's changed to 1 millisecond. Broadly speaking, a timer
cannot span more than 24.8 days.

Returns an opaque value that represents the timer.

## clearTimeout(t)

Stop a timer that was previously created with `setTimeout()`. The callback will
not execute.

## setInterval(cb, ms)

Run callback `cb` repeatedly every `ms` milliseconds. Note that the actual
interval may vary, depending on external factors like OS timer granularity and
system load. It's never less than `ms` but it may be longer.

The interval must be in the range of 1-2,147,483,647 inclusive. If the value is
outside that range, it's changed to 1 millisecond. Broadly speaking, a timer
cannot span more than 24.8 days.

Returns an opaque value that represents the timer.

## clearInterval(t)

Stop a timer that was previously created with `setInterval()`. The callback
will not execute.

<!--type=global-->

The timer functions are global variables. See the [timers][] section.

[buffer section]: buffer.html
[module section]: modules.html
[module system documentation]: modules.html
[Modules]: modules.html#modules_modules
[process object]: process.html#process_process
[console]: console.html
[timers]: timers.html
