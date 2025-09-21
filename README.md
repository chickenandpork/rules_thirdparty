# rules_thirdparty

Some third-party stuff in Bazel.  Maybe.  Let's see.

## dnsmasq
(tested on darwin/arm64 with OSX-26.0)

I first created this repo to work with Simon Kelley's dnsmasq in a cross-build environment, but I
had such a problem getting logs or details from the foreign_cc build.

Eventually, I saw that the make within the distributed Makefile was re-passing to $(MAKE) so I
wrapped that in a log; this confirmed that the build was going (quickly!) and the failure was
elsewhere.  Wrapping the `install` target helped show:

1. PREFIX is getting a bazel sandbox path anyhow.  Although DESTDIR was intended to offer an
    installroot capability, everyone follows Redhat's behavior of misusing PREFIX in automake
    builds.  I dropped DESTDIR and simply overloaded PREFIX
2. the install is working, and the PREFIX path is where `make()` expects it to be
3. checking that target, `dnsmasq` is installed to PREFIX/sbin/dnsmasq (per the BINDIR defined in
    Makefile) but `make()` is only looking in $PREFIX/bin/

Redefining BINDIR in the BUILD.dnsmasq.bazel resolved.

The debugging patch is in the thirdparty folder: `dnsmasq/Debug-the-build.patch`

