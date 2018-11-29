# boost-iostreams_test
minimal test case for boost/iostreams bug

This repo is a temporary location for a minimal test case written to try and reproduce the linking bug https://github.com/boostorg/iostreams/issues/73 .

To test:

1. clone this repository
2. Source the BuildScript file "source BuildInstall"

xz-utils, zlib, bzip2, and boost will build.

3. View the oputput of the ldd command.

