# boost-iostreams_test

This repo is a temporary location for a minimal test case written to try and reproduce the linking bug https://github.com/boostorg/iostreams/issues/73 .

To test:

1. clone this repository
2. Source the BuildScript file "source BuildInstall"
3. This will download the 1.68.0 release source from the web.
4. The included xz-utils, zlib, bzip2, will build.
5. Boost will build.
6. View the oputput of the ldd command.

 Running ldd on the boost iostreams library
 ```
	linux-vdso.so.1 =>  (0x00007ffde09a1000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f423426c000)
	libz.so.1 => /tmp/boost-test/lib/libz.so.1 (0x00007f4234050000)
	libbz2.so.1 => /tmp/boost-test/lib/libbz2.so.1 (0x00007f4233e3b000)
	liblzma.so.0 => /usr/lib64/liblzma.so.0 (0x00007f4233c1a000)        <----- This should link to /tmp/boost-test/lib/liblzma
	libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x00007f4233913000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f423368f000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f4233479000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f423325b000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f4232ec7000)
	/lib64/ld-linux-x86-64.so.2 (0x000055858ca14000)
```


