

include(ExternalProject)

set(BZIP2_DEFINES "${CMAKE_C_FLAGS_RELEASE} -D_FILE_OFFSET_BITS=64")

# build bzip2.  Included is the shared and static build sections, however,
# the shared section is the important one here.
ExternalProject_Add(bzip2
	URL  ${CMAKE_CURRENT_SOURCE_DIR}/bzip2-1.0.6.tar.xz
	URL_MD5 f3ce2ae7bf90a8f242dfe6391869f8de
	PREFIX ${CMAKE_CURRENT_BINARY_DIR}/bzip2
	INSTALL_DIR ${CMAKE_SOURCE_DIR}
	UPDATE_COMMAND ""
	CONFIGURE_COMMAND ""
	BUILD_COMMAND	
		$(MAKE) CFLAGS=${BZIP2_DEFINES}
	INSTALL_COMMAND 
		$(MAKE) PREFIX=<INSTALL_DIR> install	
	TEST_COMMAND ""
	BUILD_IN_SOURCE 1
	LOG_CONFIGURE 1
	LOG_BUILD 1
	LOG_INSTALL 1
)

ExternalProject_Add(bzip2_shared
	DEPENDS bzip2
	DOWNLOAD_COMMAND ""
	PREFIX bzip2_shared
	SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/bzip2/src/bzip2
	INSTALL_DIR ${CMAKE_SOURCE_DIR}/lib
	UPDATE_COMMAND ""
	CONFIGURE_COMMAND ""
	BUILD_COMMAND 
		$(MAKE) CFLAGS=${BZIP2_DEFINES} -f Makefile-libbz2_so
	INSTALL_COMMAND 
		${CMAKE_COMMAND} -E copy_if_different libbz2.so.1.0.6 <INSTALL_DIR>
		COMMAND ${CMAKE_COMMAND} -E chdir <INSTALL_DIR>
				${CMAKE_COMMAND} -E create_symlink libbz2.so.1.0.6 libbz2.so.1
		COMMAND ${CMAKE_COMMAND} -E chdir <INSTALL_DIR>
				${CMAKE_COMMAND} -E create_symlink libbz2.so.1.0.6 libbz2.so	
	BUILD_IN_SOURCE 1
	LOG_CONFIGURE 1
	LOG_BUILD 1
	LOG_INSTALL 1
)

# Build the lzma library from xz-utils
ExternalProject_Add(xz
	URL  ${CMAKE_CURRENT_SOURCE_DIR}/xz-5.2.4.tar.xz
	URL_MD5 9318bd17f95bbd3a9fab3a371a807b81
	PREFIX ${CMAKE_CURRENT_BINARY_DIR}/xz
	INSTALL_DIR ${CMAKE_SOURCE_DIR}
	UPDATE_COMMAND ""
	CONFIGURE_COMMAND 
		./configure 
		--prefix=<INSTALL_DIR>
		--enable-werror
		--disable-scripts	
	BUILD_IN_SOURCE 1
	LOG_CONFIGURE 1
	LOG_BUILD 1
	LOG_INSTALL 1
	TEST_COMMAND ""
	TEST_BEFORE_INSTALL 0
	TEST_EXCLUDE_FROM_MAIN 1
)

# Build zlib from source
ExternalProject_Add(zlib
	URL  ${CMAKE_CURRENT_SOURCE_DIR}/zlib-1.2.5.3.tar.xz
	URL_MD5 cc0c6838b3343530c7997627e67a9e4c
	PREFIX ${CMAKE_CURRENT_BINARY_DIR}/zlib
	INSTALL_DIR ${CMAKE_SOURCE_DIR}
	UPDATE_COMMAND ""
	CONFIGURE_COMMAND 
		./configure 		
		--prefix=<INSTALL_DIR>
		--shared
		--64 
		--libdir=<INSTALL_DIR>/lib 	
	BUILD_IN_SOURCE 1
	LOG_CONFIGURE 1
	LOG_BUILD 1
	LOG_INSTALL 1
)

ExternalProject_Add(boost
	DEPENDS xz zlib bzip2_shared
	URL https://dl.bintray.com/boostorg/release/1.68.0/source/boost_1_68_0.tar.bz2
	URL_HASH SHA256=7f6130bc3cf65f56a618888ce9d5ea704fa10b462be126ad053e80e553d6d8b7
	DOWNLOAD_DIR ${CMAKE_CURRENT_SOURCE_DIR}
	PREFIX ${CMAKE_CURRENT_BINARY_DIR}/boost	
	INSTALL_DIR ${CMAKE_SOURCE_DIR}
	UPDATE_COMMAND ""		
	CONFIGURE_COMMAND 
		./bootstrap.sh 
		--with-python-version=2.7	
		--with-toolset=gcc 
		--prefix=<INSTALL_DIR>
		--with-libraries=iostreams
	BUILD_COMMAND 
		./b2 
		--layout=system
		--build-dir=${CMAKE_CURRENT_BINARY_DIR}/boost/src/boost-build
		--stage-dir=${CMAKE_CURRENT_BINARY_DIR}/boost/src/boost-stage
		variant=release
		link=shared,static
		address-model=64		
		threading=multi
		runtime-link=shared
		-j${N}
		--prefix=<INSTALL_DIR>
		cxxflags=${CMAKE_CXX_FLAGS_RELEASE}		
		-sBZIP2_LIBRARY_PATH=<INSTALL_DIR>/lib
		-sBZIP2_NAME=bz2
		-sBZIP2_INCLUDE=<INSTALL_DIR>/include		
		-sLZMA_LIBRARY_PATH=<INSTALL_DIR>/lib
		-sLZMA_NAME=lzma						# <- Bug.  Doesn't find LZMA,
		-sLZMA_INCLUDE=<INSTALL_DIR>/include	# 		   Uses systems liblzma.
		-sZLIB_LIBRARY_PATH=<INSTALL_DIR>/lib
		-sZLIB_NAME=z
		-sZLIB_INCLUDE=<INSTALL_DIR>/include
		stage
	INSTALL_COMMAND 
		./b2 install
	LOG_UPDATE 1
	LOG_CONFIGURE 1
	LOG_BUILD 1
	LOG_INSTALL 1
	BUILD_IN_SOURCE 1		
)
