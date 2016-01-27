## `fbsimctl`

`fbsimctl` is a command line interface to the `FBSimulatorControl` Framework. It intends to expose the core features of the `FBSimulatorControl` framework with a usage pattern that accommodates many common manual and automation scenarios.

## Rationale

The `FBSimulatorControl` exposes a lot of functionality over it's API surface. The Framework can be dropped in to Mac OS X Application and Test Projects, however this may be inconvenient some developers who just want a simple way of having access to a number of features.

`fbsimctl` is Command Line Application written in Swift that links with the `FBSimulatorControl` Framework. Swift is well-suited to writing Command-Line Applications and the interoperability with Objective-C means that `fbsimctl` can have a small footprint, instead choosing to use as much of the `FBSimulatorControl` Framework as possible. Where `FBSimulatorControl` has a very configurable API, `fbsimctl` takes more of a 'batteries-included' approach to interacting with Simulators. This means that `fbsimctl` will choose reasonable defaults where appropriate.

## Interface

`fbsimctl` 

`fbsimctl` has a key number of components to the user interface:
- `Configuration` this is a global setting per-process and defines global constants such as the `SimDeviceSet` to use as well as switches for logging.
- `Query``: A predicate that specifies the Simulators to which to apply an interaction to
- `Format`: An optional set of arguments that specifies the properties of a Simulator that will be 
- `Interaction`: The possible ways of interacting with a Simulator.

`fbsimctl` also supports binding to the interactive prompt via a socket, however this feature is still experimental.

## Examples




## Interactive Mode

`fbsimctl` supports an interactive mode, which means that commands can be input to a prompt. In this mode, you can take advantage of the fact that `fbsimctl` will remember the Simulators that you last interacted with:

```
fbsimctl -i // Launches fbsimctl in 'interactive' mode.
--state=shutdown list // Lists all shutdown Simulators
271BF53B-778F-4409-83DB-DB8C86B75DAC boot // Boots the specified Simulator
install /Path/To/Some.app // Installs 'Some.app' on the Simulator
launch /Path/To/Some.app // Launches 'Some.app' on the Simulator.
```

## Installation

`fbsimctl` can be built directly from the repo without any external dependencies. Just build the `fbsimctl` scheme in the `fbsimctl.xcworkspace` via Xcode or `xcodebuild`:

```xcodebuild -workspace fbsimctl/fbsimctl.xcworkspace -scheme FBSimulatorControlKit -derivedDataPath foobar build```

The `build.sh` script at the repo does the same, but with [`xctool`](https://github.com/facebook/xctool):

`build.sh cli`

[A Homebrew formula is being worked on](https://github.com/facebook/FBSimulatorControl/issues/120).

## Output

`fbsimctl` outputs information about the current command to `stdout` and logs the standard `FBSimulatorControl` informational logs to `stderr`. The verbosity of these messages can be increased with the `--debug-logging` flag.

`fbisimctl` can play nice with other programming languages, by supporting line-terminated JSON output. Setting the `--json` flag will result in logging and informational messages to be written to `stderr during the progression of a command.

## `FBSimulatorControlKit`

As Xcode can't test Executables directly a separate Framework target `FBSimulatorControlKit` is used to contain the core functionality of `fbsimctl`. These components are tested in the`FBSimulatorControlKitTests` target.

Unfortunately, it is currently not possible for user-provided Frameworks to be linked into Swift Command Line Executables. The [Swift Standard Libraries](https://developer.apple.com/library/ios/qa/qa1881/_index.html) is copied into the Framework's bundle as a`dylib`s for `FBSimulatorControlKit` and duplicated in via embedding in the `fbsimctl` binary. 

To work around this the relevant sources are compiled for the `FBSimulatorControlKit` framework as well as the `fbsimctl` executable targets. This means that:
1) `fbsimctl` can use the sources without duplicating symbols from a linked `FBSimulatorControlKit`.
2) `FBSimulatorControlKit` is fully testable, with it's own test target.
