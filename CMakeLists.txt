cmake_minimum_required (VERSION 2.8)
cmake_policy(VERSION 2.8)
project(ambition-plugin-gredis)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake )
enable_testing()

find_package(Vala REQUIRED)
include(ValaVersion)
include(ValaPrecompile)
ensure_vala_version("0.22.0" MINIMUM)

find_package(Ambition REQUIRED)

IF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
	SET(CMAKE_INSTALL_PREFIX "/usr" CACHE PATH "install prefix" FORCE)
ENDIF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

IF(LIB_INSTALL_DIR)
ELSE()
SET(LIB_INSTALL_DIR lib)
ENDIF()

set (PPKGNAME ambition-plugin-gredis)
set (PLUGINNAME GRedis)
set (PPKGVERSION 0.1.0)
set (PREFIX ${CMAKE_INSTALL_PREFIX})
set (DOLLAR "$")

SET( APGR_DEPS_PACKAGES
	glib-2.0
	gee-0.8
	gmodule-2.0
	${AMBITION_LIBNAME}
	gredis-0.1
	hiredis
)

SET( APGR_VALA_PACKAGES
	${APGR_DEPS_PACKAGES}
	posix
)

find_package(PkgConfig)
pkg_check_modules(APGR_DEPS REQUIRED ${APGR_DEPS_PACKAGES})
SET( CMAKE_BUILD_TYPE Debug )
SET( APGR_DEPS_CFLAGS_ALL
	${APGR_DEPS_CFLAGS}
	${APGR_DEPS_CFLAGS_OTHER}
)
set(APGR_LIB_PATHS
    ${APGR_DEPS_LIBRARY_DIRS}
)

set (VAPIDIRS
	--vapidir='${CMAKE_SOURCE_DIR}'
	--vapidir='${CMAKE_SOURCE_DIR}/src'
	--vapidir='${CMAKE_SOURCE_DIR}/vapi'
	--vapidir='${BINSOURCE}'
)
set (DEFAULT_VALA_OPTIONS
	${VAPIDIRS}
	--thread
	--enable-experimental
	--target-glib=2.32
	--debug
	-g
)

add_subdirectory (gredis)
add_subdirectory (src)
add_subdirectory (test)

install (
	FILES manifest.json documentation.md
	DESTINATION ${CMAKE_INSTALL_PREFIX}
)
