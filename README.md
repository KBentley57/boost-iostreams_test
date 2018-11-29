# boost-iostreams_test

This repo is a temporary location for a minimal test case written to try and reproduce the linking bug https://github.com/boostorg/iostreams/issues/73 .

To test:

1. clone this repository
2. Source the BuildScript file "source BuildInstall"
2.1 This will download the 1.68.0 release source.
2.2 xz-utils, zlib, bzip2, and boost will build.
3. View the oputput of the ldd command.

