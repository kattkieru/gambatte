cmake_minimum_required(VERSION 3.4)
project(gbdk3-gambatte)

add_subdirectory( ${CMAKE_CURRENT_SOURCE_DIR}/../sdl ${CMAKE_CURRENT_BINARY_DIR}/extern )

add_definitions( -DHAVE_STDINT_H )

file( 
	GLOB LIBGAMBATTE_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/src/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/src/file/file.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/src/mem/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/src/sound/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/src/video/*.cpp
)

file( 
	GLOB GAMBATTE_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/gambatte_sdl/src/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/common/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/common/resample/src/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/common/videolink/*.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/common/videolink/vfilters/*.cpp
)

if (WIN32)
#	message( FATAL_ERROR "Not yet supported." )
	add_definitions( -D_CRT_SECURE_NO_WARNINGS -D_CRT_SECURE_NO_DEPRECATE -D_WINDOWS -D_WIN32_WINNT=0x0400 )
	set( PLATFORM_LIBRARIES opengl32.lib winmm.lib dxguid.lib )
	message( AUTHOR_WARNING "+ Building for Windows." )
	set( MACHINE_FLAG "/MACHINE:x86" )

elseif( UNIX AND APPLE )
	set( MACHINE_FLAG "-m32" )

	set( CFLAGS "-Wall -Wextra -O2 -g -fomit-frame-pointer -fPIC" )
	set( CXXFLAGS "${CFLAGS} -fno-exceptions -fno-rtti" )
	add_definitions( -D_GNU_SOURCE=1 -D_THREAD_SAFE )
	set( PLATFORM_LIBRARIES "-Wl,-framework,OpenGL -Wl,-framework,Cocoa -Wl,-framework,ApplicationServices -Wl,-framework,Carbon -Wl,-framework,AudioToolbox -Wl,-framework,AudioUnit -Wl,-framework,IOKit" )
	message( AUTHOR_WARNING "+ Building for macOS." )
else()
	message( FATAL_ERROR "Unknown operating system. Aborting." )
endif()

add_library( libgambatte ${LIBGAMBATTE_SOURCES} )
target_include_directories( libgambatte PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/common ${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/src ${CMAKE_CURRENT_SOURCE_DIR}/libgambatte/include )
set_target_properties(libgambatte PROPERTIES COMPILE_FLAGS ${MACHINE_FLAG} LINK_FLAGS ${MACHINE_FLAG})


add_executable( gambatte ${GAMBATTE_SOURCES} )
target_link_libraries( gambatte PUBLIC libgambatte sdl sdlmain ${PLATFORM_LIBRARIES} )
set_target_properties( gambatte PROPERTIES COMPILE_FLAGS ${MACHINE_FLAG} LINK_FLAGS ${MACHINE_FLAG} )

