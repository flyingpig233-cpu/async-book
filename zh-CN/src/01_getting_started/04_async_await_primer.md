# `async` / `.await`入门

`async` / `.await`是 Rust 的内置工具，用于编写看起来像同步代码的异步函数。 `async`将代码块转换为实现名为`Future`特征的状态机。而在同步方法中调用阻塞函数会阻塞整个线程，而被阻塞的`Future`将放弃对线程的控制，从而允许其他`Future`运行。

让我们向`Cargo.toml`文件添加一些依赖项：

```toml
{{#include ../../examples/01_04_async_await_primer/Cargo.toml:9:10}}
```

要创建异步函数，可以使用`async fn`语法：

```rust,edition2018
async fn do_something() { /* ... */ }
```

`async fn`返回的值是一个`Future` 。任何事情的发生都需要在执行器上运行`Future` 。

```rust,edition2018
{{#include ../../examples/01_04_async_await_primer/src/lib.rs:hello_world}}
```

在`async fn`中，你可以使用`.await`等待另一个实现`Future`特征的类型完成，例如另一个`async fn`的输出。与`block_on`不同， `.await`不会阻塞当前线程，而是异步等待 Future 完成，如果 Future 当前无法取得进展，则允许其他任务运行。

例如，假设我们有三个`async fn` ： `learn_song` 、 `sing_song`和`dance` ：

```rust,ignore
async fn learn_song() -> Song { /* ... */ }
async fn sing_song(song: Song) { /* ... */ }
async fn dance() { /* ... */ }
```

One way to do learn, sing, and dance would be to block on each of these individually:

```rust,ignore
{{#include ../../examples/01_04_async_await_primer/src/lib.rs:block_on_each}}
```

但是，我们无法通过这种方式提供最佳性能——我们一次只能做一件事！显然，我们必须先学习这首歌，然后才能唱歌，但可以在学习和唱歌的同时跳舞。为此，我们可以创建两个可以同时运行的独立`async fn` ：

```rust,ignore
{{#include ../../examples/01_04_async_await_primer/src/lib.rs:block_on_main}}
```

In this example, learning the song must happen before singing the song, but both learning and singing can happen at the same time as dancing. If we used `block_on(learn_song())` rather than `learn_song().await` in `learn_and_sing`, the thread wouldn't be able to do anything else while `learn_song` was running. This would make it impossible to dance at the same time. By `.await`-ing the `learn_song` future, we allow other tasks to take over the current thread if `learn_song` is blocked. This makes it possible to run multiple futures to completion concurrently on the same thread.
