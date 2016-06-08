cmake_minimum_required (VERSION 3.0)

if(WIN32)
	# tell V$ that we're going to use functions that have been standard since the 80s (even if they have sharp edges)
	add_definitions(-D_CRT_SECURE_NO_WARNINGS)
	add_definitions(-D_SCL_SECURE_NO_WARNINGS)

	foreach(flag_var CMAKE_C_FLAGS_DEBUG CMAKE_CXX_FLAGS_DEBUG CMAKE_C_FLAGS_RELEASE CMAKE_CXX_FLAGS_RELEASE CMAKE_C_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_C_FLAGS_RELWITHDEBINFO CMAKE_CXX_FLAGS_RELWITHDEBINFO)
		string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
		string(REGEX REPLACE "/MDd" "/MTd" ${flag_var} "${${flag_var}}")
	endforeach(flag_var)
	SET (CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG}" CACHE STRING "MSVC C Debug MT flags " FORCE)
	SET (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG}" CACHE STRING "MSVC CXX Debug MT flags " FORCE)
	SET (CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE}" CACHE STRING "MSVC C Release MT flags " FORCE)
	SET (CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE}" CACHE STRING "MSVC CXX Release MT flags " FORCE)
	SET (CMAKE_C_FLAGS_MINSIZEREL "${CMAKE_C_FLAGS_MINSIZEREL}" CACHE STRING "MSVC C Debug MT flags " FORCE)
	SET (CMAKE_CXX_FLAGS_MINSIZEREL "${CMAKE_CXX_FLAGS_MINSIZEREL}" CACHE STRING "MSVC C Release MT flags " FORCE)
	SET (CMAKE_C_FLAGS_RELWITHDEBINFO "${CMAKE_C_FLAGS_RELWITHDEBINFO}" CACHE STRING "MSVC CXX Debug MT flags " FORCE)
	SET (CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO}" CACHE STRING "MSVC CXX Release MT flags " FORCE)
else(WIN32)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -std=gnu++11 -static-libstdc++")
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static-libstdc++")
endif(WIN32)

add_definitions(-DCMAKE_BINARY_DIR="${CMAKE_BINARY_DIR}")
add_definitions(-DCMAKE_SOURCE_DIR="${CMAKE_SOURCE_DIR}")

project(Quadifier)

FIND_PACKAGE(OpenGL)

include_directories(
	win32/common
	common
)
add_executable(
	launcher WIN32
    win32/common/DLLInject.cpp
    win32/common/GLWindow.cpp
    win32/common/NtCreateThreadEx.cpp
    win32/source/main.cpp
    win32/common/DLLInject.h
    win32/common/GLWindow.h
    win32/common/NtCreateThreadEx.h
	)
	TARGET_LINK_LIBRARIES(launcher ${OPENGL_LIBRARIES})


include_directories(
	win32/extern/mhook-2.3
	win32/common
	common
)

add_library(
	module SHARED
    common/CriticalSection.cpp
    common/Event.cpp
    win32/common/DebugUtil.cpp
    win32/common/GLWindow.cpp
    win32/common/Log.cpp
    win32/common/StereoUtil.cpp
    win32/common/WinMessage.cpp
    win32/module/source/DXGIAdapterProxy.cpp
    win32/module/source/DXGIDeviceProxy.cpp
    win32/module/source/DXGIFactory1Proxy.cpp
    win32/module/source/DXGIFactoryProxy.cpp
    win32/module/source/DXGISwapChainProxy.cpp
    win32/module/source/Extensions.cpp
    win32/module/source/ID3D11DeviceContextProxy.cpp
    win32/module/source/ID3D11DeviceProxy.cpp
    win32/module/source/IDirect3D9Proxy.cpp
    win32/module/source/IDirect3DDevice9Proxy.cpp
    win32/module/source/main.cpp
    win32/module/source/Quadifier.cpp
    win32/extern/mhook-2.3/disasm-lib/cpu.c
    win32/extern/mhook-2.3/disasm-lib/disasm.c
    win32/extern/mhook-2.3/disasm-lib/disasm_x86.c
    win32/extern/mhook-2.3/mhook-lib/mhook.cpp
    win32/extern/mhook-2.3/disasm-lib/misc.c
    win32/module/source/Settings.cpp
    common/CriticalSection.h
    common/Event.h
    win32/common/DebugUtil.h
    win32/common/GLWindow.h
    win32/common/Log.h
    win32/common/StereoUtil.h
    win32/common/WinMessage.h
    win32/module/source/DXGIAdapterProxy.h
    win32/module/source/DXGIDeviceProxy.h
    win32/module/source/DXGIFactory1Proxy.h
    win32/module/source/DXGIFactoryProxy.h
    win32/module/source/DXGISwapChainProxy.h
    win32/module/source/Extensions.h
    win32/module/source/ID3D11DeviceContextProxy.h
    win32/module/source/ID3D11DeviceProxy.h
    win32/module/source/IDirect3D9Proxy.h
    win32/module/source/IDirect3DDevice9Proxy.h
    win32/module/source/Quadifier.h
    win32/extern/mhook-2.3/disasm-lib/cpu.h
    win32/extern/mhook-2.3/disasm-lib/disasm.h
    win32/extern/mhook-2.3/disasm-lib/disasm_x86.h
    win32/extern/mhook-2.3/disasm-lib/disasm_x86_tables.h
    win32/extern/mhook-2.3/mhook-lib/mhook.h
    win32/extern/mhook-2.3/disasm-lib/misc.h
    win32/module/source/Settings.h
  )
	set_property(TARGET module APPEND PROPERTY
	  COMPILE_DEFINITIONS UNICODE _UNICODE
	)
	TARGET_LINK_LIBRARIES(
		module
			$ENV{DXSDK_DIR}/Lib/x86/d3d9.lib
			${OPENGL_LIBRARIES}
			setupapi
		)
	#<AdditionalLibraryDirectories>$(DXSDK_DIR)/Lib/x86</AdditionalLibraryDirectories>
		#<AdditionalDependencies>d3d9.lib;opengl32.lib;setupapi.lib;%(AdditionalDependencies)</AdditionalDependencies>
