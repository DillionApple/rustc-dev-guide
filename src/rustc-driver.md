# The Rustc Driver and Interface

The [`rustc_driver`] is essentially `rustc`'s `main()` function. It acts as
the glue for running the various phases of the compiler in the correct order,
using the interface defined in the [`rustc_interface`] crate.

The `rustc_interface` crate provides external users with an (unstable) API
for running code at particular times during the compilation process, allowing
third parties to effectively use `rustc`'s internals as a library for
analysing a crate or emulating the compiler in-process (e.g. the RLS or rustdoc).

For those using `rustc` as a library, the [`rustc_interface::interface::run_compiler()`][i_rc]
function is the main entrypoint to the compiler. It takes a configuration for the compiler
and a closure that takes a [`Compiler`]. `run_compiler` creates a `Compiler` from the 
configuration and passes it to the closure. Inside the closure, you can use the `Compiler`
to drive queries to compile a crate and get the results. This is what the `rustc_driver` does too.

You can see what queries are currently available through the rustdocs for [`Compiler`].
You can see an example of how to use them by looking at the `rustc_driver` implementation,
specifically the [`rustc_driver::run_compiler` function][rd_rc] (not to be confused with
[`rustc_interface::interface::run_compiler`][i_rc]). The `rustc_driver::run_compiler` function 
takes a bunch of command-line args and some other configurations and
drives the compilation to completion.

`rustc_driver::run_compiler` also takes a [`Callbacks`][cb]. In the past, when
the `rustc_driver::run_compiler` was the primary way to use the compiler as a
library, these callbacks were used to have some custom code run after different
phases of the compilation. If you read [Appendix A], you may notice the use of the
types `CompilerCalls` and `CompileController`, which no longer exist. `Callbacks`
replaces this functionality.

> **Warning:** By its very nature, the internal compiler APIs are always going
> to be unstable. That said, we do try not to break things unnecessarily.


[cb]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_driver/trait.Callbacks.html
[rd_rc]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_driver/fn.run_compiler.html
[i_rc]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_interface/interface/fn.run_compiler.html
[`rustc_interface`]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_interface/index.html
[`rustc_driver`]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_driver/
[`Compiler`]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_interface/interface/struct.Compiler.html
[`Session`]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_session/struct.Session.html
[`TyCtxt`]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc/ty/struct.TyCtxt.html
[`SourceMap`]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_span/source_map/struct.SourceMap.html
[stupid-stats]: https://github.com/nrc/stupid-stats
[Appendix A]: appendix/stupid-stats.html
