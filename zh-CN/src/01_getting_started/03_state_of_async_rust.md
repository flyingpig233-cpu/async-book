# 异步 Rust 的状态

异步 Rust 的部分功能与同步 Rust 一样，具有同样的稳定性保证。其他部分仍在完善中，将随着时间的推移而发生变化。使用异步 Rust，您可以期待：

- 对于典型的并发工作负载具有出色的运行时性能。
- 与高级语言功能（如生命周期和固定）进行更频繁的交互。
- 同步和异步代码之间以及不同异步运行时之间存在一些兼容性限制。
- 由于异步运行时和语言支持的不断发展，维护负担更高。

简而言之，异步 Rust 比同步 Rust 更难使用，维护负担也更高，但回报却是一流的性能。异步 Rust 的所有领域都在不断改进，因此这些问题的影响会随着时间的推移逐渐消失。

## 语言和库支持

虽然 Rust 本身支持异步编程，但大多数异步应用程序依赖于社区包提供的功能。因此，你需要依赖多种语言功能和库支持：

- 标准库提供了最基本的特征、类型和功能，例如[`Future`](https://doc.rust-lang.org/std/future/trait.Future.html)特征。
- Rust 编译器直接支持`async/await`语法。
- [`futures`](https://docs.rs/futures/)包提供了很多实用类型、宏和函数。它们可以在任何异步 Rust 应用程序中使用。
- 异步代码的执行、IO 和任务生成由“异步运行时”提供，例如 Tokio 和 async-std。大多数异步应用程序和一些异步包都依赖于特定的运行时。有关更多详细信息，请参阅[“异步生态系统”](../08_ecosystem/00_chapter.md)部分。

您可能习惯于使用同步 Rust 中的某些语言功能，但异步 Rust 中尚不可用。值得注意的是，Rust 不允许您在特征中声明异步函数。相反，您需要使用变通方法来实现相同的结果，这可能会更加冗长。

## 编译和调试

在大多数情况下，异步 Rust 中的编译器错误和运行时错误与 Rust 中的工作方式相同。但有几个值得注意的区别：

### 编译错误

异步 Rust 中的编译错误符合与同步 Rust 相同的高标准，但由于异步 Rust 通常依赖于更复杂的语言特性，例如生命周期和固定，因此您可能会更频繁地遇到这些类型的错误。

### 运行时错误

每当编译器遇到异步函数时，它都会在后台生成一个状态机。异步 Rust 中的堆栈跟踪通常包含来自这些状态机的详细信息，以及来自运行时的函数调用。因此，解释堆栈跟踪可能比在同步 Rust 中更复杂一些。

### 新的故障模式

异步 Rust 中可能存在一些新奇的故障模式，例如，如果您从异步上下文调用阻塞函数，或者错误地实现了`Future`特征。此类错误可以悄无声息地通过编译器，有时甚至单元测试。本书旨在让您牢固理解底层概念，这可以帮助您避免这些陷阱。

## 兼容性注意事项

异步和同步代码并不总是可以自由组合的。例如，您不能直接从同步函数调用异步函数。同步和异步代码也倾向于推广不同的设计模式，这可能会使编写针对不同环境的代码变得困难。

即使是异步代码也不总是可以自由组合。一些 crate 依赖于特定的异步运行时才能运行。如果是这样，通常会在 crate 的依赖列表中指定它。

这些兼容性问题可能会限制您的选择，因此请务必尽早研究您可能需要哪种异步运行时和哪些包。一旦您确定了运行时，您就不必太担心兼容性了。

## 性能特征

异步 Rust 的性能取决于您使用的异步运行时的实现。尽管支持异步 Rust 应用程序的运行时相对较新，但它们在大多数实际工作负载下的表现都非常出色。

话虽如此，大多数异步生态系统都假设*多线程*运行时。这使得很难享受单线程异步应用程序的理论性能优势，即更便宜的同步。另一个被忽视的用例是*延迟敏感任务*，这对驱动程序、GUI 应用程序等很重要。此类任务依赖于运行时和/或操作系统支持才能进行适当调度。您可以期待未来对这些用例有更好的库支持。
