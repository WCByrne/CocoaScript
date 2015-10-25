
# Diamond - Swift scripting made easy

I, [like](https://realm.io/news/swift-for-rubyists/) [many](https://realm.io/news/swift-scripting/)
would love to see Swift as the premier scripting language for the Mac (and Linux?)
As it stands however, using [xcrun swift](http://nomothetis.svbtle.com/swift-for-scripting)
has it's limitations:

* The Xcode editor for swift scripts does not allow auto-completion
* managing dependencies on frameworks is left to the user
* In the even of a crash the line number is not reported
* It is not possible to run swift scripts in the debugger
* Code can not be shared readily between swift scripts
* A script can not have a UI component using Cocoa
* Foundation is not convenient when working with files
* Swift Strings are uncompromising and don't support Regexps

`diamond` is a small binary intended to be used as a Swift interpreter to alleviate these
problems. A small Ruby script looks after converting your script into a Xcode project and
building it (along with any dependencies) as required. It also comes with a small library
`SwiftRuby` which replicates the core of the Ruby api to work more easily with files,
Strings and Regular Expressions than would otherwise be the case with Foundation.

### Xcode Editor, auto-completion and dependency management

![Icon](http://injectionforxcode.johnholdsworth.com/completion.png)

To create a script type `diamond <path_to_script>`. `diamond` will create an Xcode
project for the script. This project can be accessed by typing `<path_to_script> -edit`.
Your script will be shadowed by the file main.swift in this project. Frameworks can be
pulled into your script from CocoaPods or Carthage using the syntax above and diamond
will download and build them placing them in ~/Library/Diamond/Frameworks.
As the script project has it's `Framework search path` is set to include 
`~/Library/Diamond/Frameworks` auto-completion now works.

### Capturing script stacktraces

![Icon](http://injectionforxcode.johnholdsworth.com/stacktrace.png)

`diamond` runs your script from a parent process `guardian` to check
for crashes. This process finds the Symbolicated CrashReporter ".crash" file
and formats it to extract the stacktrace, de-mangling any Swift function names.

### Running Diamond scripts in the debugger

![Icon](http://injectionforxcode.johnholdsworth.com/debugging.png)

When working with the Xcode project for a script it's "Binary" target can be run
in the lldb debugger as you would a normal program. The binary target can also be
used to create standalone binary versions of a script provided any dependencies
are available. If you are debugging a script which imports another script or a 
framework you can get warnings about duplications of Swift Classes as they
are both built into the binary and linked against by the frameworks.
You can ignore these.

### Importing one script into another to share code

`diamond's` script projects are actually built as frameworks and run by loading
them as a bundle. This means they can be imported into each other. If you wish to
share some script library code it should be in the scripts's directory ~/bin or
~/bin/lib for it to be rebuilt automatically. Otherwise if you want to use external
frameworks the `-F`, `-L`, `-l<library>` and `-Xlinker` options can be added at the
top of the script as they can with `xcrun swift`.

### Scripts with a UI Component.

Script projects come with a `MainMenu.xib` and `AppDelegate.swift` by default so all that's
required to give a script a UI is to call `NSApplicationMain` as shown in the `browse`
example included in the distribution. For this to work `diamond` creates a dummy `Contents`
directory in `~/bin` where the `diamond` binary resides when you run the script.

### import SwiftRuby for easier access to files and Strings

Foundation is not a particularly convenient way to work with files and processes
and Swift's uncompromising String class does not make accessing parts of a string
easy. To resolve this a port of the Core Ruby apis has been made to Swift in the
project [SwiftRuby](https://github.com/RubyNative/SwiftRuby). Classes: File, Stat,
Time and Regexp are included along with extensions to String and Array to round off
their rather Austere edges.

### Requirements

To use Diamond, download and build this project and make sure that have `$HOME/bin`
(`~/bin` does not work) in your UNIX `$PATH`. For some reason you may have to retry 
the build if you are using `El Capitan`.

You can then type `diamond path_to_script` and it creates a blank script, an Xcode
framework project then builds and runs it. If you prefer editing in Xcode type
`path_to_script -edit` to open the auto-created project. To get started there
is a small example script `browse` in the project directory.

To use dependencies the `CocoaPods` gem and it's `Rome` plugin need to be installed.

```
    $ sudo gem install cocoapods
    $ sudo gem install cocoapods-rome
```

Use a !pod comment in framework import to force updating a particular pod later.

### Reloader

Diamond contains an implementation of code injection. If you are running a
UI script and update one of it's sources it will be built into a bundle
and loaded applying any changes to class method implementations without
requiring a restart.

The author can be reached on Twitter
[@Injection4Xcode](https://twitter.com/#!/@Injection4Xcode) or by email
on support at injectionforxcode.com

### MIT License

Copyright (c) 2015 John Holdsworth

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
