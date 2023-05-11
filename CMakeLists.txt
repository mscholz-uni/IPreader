cmake_minimum_required (VERSION 3.2)
enable_language (C)
project (tiffread1)

include (ExternalProject)
ExternalProject_Add(libtiff
	PREFIX 3rd-party
# --Download step---
	DOWNLOAD_NAME libtiff
	URL https://github.com/vadz/libtiff/archive/Release-v4-0-9.zip
	URL_HASH SHA1=95c8ea7ec3509a9c76b30abc46287fd104717559
# --Configure step---
	CMAKE_ARGS -DBUILD_SHARED_LIBS=OFF -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_POSITION_INDEPENDENT_CODE=ON
                   -Djpeg=OFF -Dzlib=OFF -Djbig=OFF -Dlzma=OFF -Djpeg12=OFF -Dcxx=OFF
# --Install step---
	UPDATE_COMMAND ""
	INSTALL_COMMAND ""
)
add_library(libtiffstatic STATIC IMPORTED)
set(LIBTIFF_LIBDIR ${CMAKE_CURRENT_BINARY_DIR}/3rd-party/src/libtiff-build/libtiff)
set(LIBTIFF_INCDIR ${CMAKE_CURRENT_BINARY_DIR}/3rd-party/src/libtiff/libtiff)
set_target_properties(libtiffstatic PROPERTIES
	IMPORTED_LOCATION ${LIBTIFF_LIBDIR}/${CMAKE_STATIC_LIBRARY_PREFIX}tiff${CMAKE_STATIC_LIBRARY_SUFFIX})

include (FindTclStub)
include_directories (${TCL_INCLUDE_PATH})
include_directories (${LIBTIFF_INCDIR} ${LIBTIFF_LIBDIR})
add_library (${PROJECT_NAME} MODULE libtiffread-1.c)
set_target_properties (${PROJECT_NAME} PROPERTIES PREFIX "")

include (CheckFunctionExists)
check_function_exists (pow POW_EXISTS)
if (NOT POW_EXISTS)
	list (APPEND CMAKE_REQUIRED_LIBRARIES m)
	check_function_exists (pow POW_EXISTS_IN_LIBM)
	if (POW_EXISTS_IN_LIBM)
		target_link_libraries (${PROJECT_NAME} m)
	else()
		message (FATAL_ERROR "No pow() found")
	endif()
endif()

target_link_libraries (${PROJECT_NAME} ${TCL_LIBRARY} ${TCL_STUB_LIBRARY})
target_link_libraries (${PROJECT_NAME} libtiffstatic)
add_dependencies(${PROJECT_NAME} libtiff)
