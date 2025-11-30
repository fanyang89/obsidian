## await_suspend

`await_ready` 返回 `false` 时，协程就挂起了。这时候协程的局部变量和挂起点都会被存入协程的状态当中，`await_suspend` 被调用到。

```c++
??? await_suspend(std::coroutine_handle<> coroutine_handle);
```

参数 `coroutine_handle` 用来表示当前协程，我们可以在稍后合适的时机通过调用 `resume` 来恢复执行当前协程：

```c++
coroutine_handle.resume();
```

注意到 `await_suspend` 函数的返回值类型我们没有明确给出，因为它有以下几种选项：

- 返回 `void` 类型或者返回 `true`，表示当前协程挂起之后将执行权还给当初调用或者恢复当前协程的函数。
- 返回 `false`，则恢复执行当前协程
  - 注意此时不同于 `await_ready` 返回 `true` 的情形，此时协程已经挂起，`await_suspend` 返回 `false` 相当于挂起又立即恢复。
- 返回其他协程的 `coroutine_handle` 对象，这时候返回的 `coroutine_handle` 对应的协程被恢复执行。
- 抛出异常，此时当前协程恢复执行，并在当前协程当中抛出异常。
  可见，`await_suspend` 支持的情况非常多，也相对复杂。实际上这也是 C++ 协程当中最为核心的函数之一了。
