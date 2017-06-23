[![Travis][travisimg]][travisurl]
[![AppVeyor][appveyorimg]][appveyorurl]
[![GoDoc][docimg]][docurl]

[travisimg]:   https://travis-ci.org/karalabe/hid.svg?branch=master
[travisurl]:   https://travis-ci.org/karalabe/hid
[appveyorimg]: https://ci.appveyor.com/api/projects/status/plroy54odykb0ch3/branch/master?svg=true
[appveyorurl]: https://ci.appveyor.com/project/karalabe/hid
[docimg]:      https://godoc.org/github.com/karalabe/hid?status.svg
[docurl]:      https://godoc.org/github.com/karalabe/hid

# Gopher Interface Devices (USB HID)

The `hid` package is a cross platform library for accessing and communicating with USB Human Interface
Devices (HID). It is an alternative package to [`gousb`](https://github.com/karalabe/gousb) for use
cases where devices support this ligher mode of operation (e.g. input devices, hardware crypto wallets).

The package wraps [`hidapi`](https://github.com/signal11/hidapi) for accessing OS specific USB HID APIs
directly instead of using low level USB constructs, which might have permission issues on some platforms.
On Linux the package also wraps [`libusb`](https://github.com/libusb/libusb). Both of these dependencies
are vendored directly into the repository and wrapped using CGO, making the `hid` package self-contained
and go-gettable.

Supported platforms at the moment are Linux, macOS and Windows (exclude constraints are also specified
for Android and iOS to allow smoother vendoring into cross platform projects).

## Cross-compiling the binaries

When you do `go get`, the embedded C library is compiled using your host OS compiler into the binary format
of your host OS. When you will try to cross-compile your program, like so:

`GOARCH=arm go build`
 
you will find (after transferring the binary to the target OS) that the call to Enumerate() command yields no
results. However, there is a way to change this. You will need to have the cross-compiler for your target
machine. I'll use an example of raspberry-pi 3.

you can get the cross-compiler from raspberry pi repository, like so:

`git clone https://github.com/raspberrypi/tools`

next, you can proceed to cross-compile the project:

`CGO_ENABLED=1 GOARCH=arm CC=tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-gcc go build`

which will produce a binary compatible with raspberry-pi.

if your host OS is a 32-bit one, just remove `-x64` from the command-line above.

as you can see, as long as you have the copy of a cross-compiler, producing binaries for different targets is not so complicated

## Acknowledgements

Although the `hid` package is an implementation from scratch, it was heavily inspired by the existing
[`go.hid`](https://github.com/GeertJohan/go.hid) library, which seems abandoned since 2015; is incompatible
with Go 1.6+; and has various external dependencies. Given its inspirational roots, I thought it important
to give credit to the author of said package too.

Wide character support in the `hid` package is done via the [`gowchar`](https://github.com/orofarne/gowchar)
library, unmaintained since 2013; non buildable with a modern Go release and failing `go vet` checks. As
such, `gowchar` was also vendored in inline (copyright headers and origins preserved).

## License

The components of `hid` are licensed as such:

 * `hidapi` is released under the [3-clause BSD](https://github.com/signal11/hidapi/blob/master/LICENSE-bsd.txt) license.
 * `libusb` is released under the [GNU LGPL 2.1](https://github.com/libusb/libusb/blob/master/COPYING)license.
 * `go.hid` is released under the [2-clause BSD](https://github.com/GeertJohan/go.hid/blob/master/LICENSE) license.
 * `gowchar` is released under the [3-clause BSD](https://github.com/orofarne/gowchar/blob/master/LICENSE) license.

Given the above, `hid` is licensed under GNU LGPL 2.1 or later on Linux and 3-clause BSD on other platforms.
