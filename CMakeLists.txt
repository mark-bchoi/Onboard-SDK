# *  @Copyright (c) 2016-2017 DJI
# *
# * Permission is hereby granted, free of charge, to any person obtaining a copy
# * of this software and associated documentation files (the "Software"), to deal
# * in the Software without restriction, including without limitation the rights
# * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# * copies of the Software, and to permit persons to whom the Software is
# * furnished to do so, subject to the following conditions:
# *
# * The above copyright notice and this permission notice shall be included in
# * all copies or substantial portions of the Software.
# *
# * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# * SOFTWARE.
# *
# *

cmake_minimum_required(VERSION 3.8)
project(onboardsdk)

set(CMAKE_VERBOSE_MAKEFILE OFF)

set(DJIOSDK 0)
set(DJIOSDK_MINOR_VERSION 1)
set(DJIOSDK_PATCH_VERSION 0)
set(DJIOSDK_VERSION
  ${DJIOSDK_MAJOR_VERSION}.${DJIOSDK_MINOR_VERSION}.${DJIOSDK_PATCH_VERSION})
add_definitions(-DDJIOSDK_MAJOR_VERSION=4)
add_definitions(-DDJIOSDK_MINOR_VERSION=1)
add_definitions(-DDJIOSDK_PATCH_VERSION=0)

add_definitions(-DDJIOSDK_IS_DEBUG=1)

if(CMAKE_SYSTEM_PROCESSOR MATCHES "i386|i686|x86|AMD64|x86_64")
   set(ARCH x86)
   add_definitions(-DDJIOSDK_HARDWARE_TYPE=3)
elseif((CMAKE_SYSTEM_PROCESSOR MATCHES "^arm*") OR (CMAKE_SYSTEM_PROCESSOR MATCHES "^aarch"))
  if(CMAKE_SIZEOF_VOID_P EQUAL 4)
     set(ARCH armv7)
     add_definitions(-DDJIOSDK_HARDWARE_TYPE=1)
  elseif(CMAKE_SIZEOF_VOID_P EQUAL 8)
     set(ARCH armv8)
     add_definitions(-DDJIOSDK_HARDWARE_TYPE=2)
  endif()
elseif()
  add_definitions(-DDJIOSDK_HARDWARE_TYPE=0)
endif()

set(RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/libs)

set(ADVANCED_SENSING 1)
set(WAYPT2_CORE 1)

if (ADVANCED_SENSING)
  add_definitions(-DADVANCED_SENSING)
endif()

if (WAYPT2_CORE)
    add_definitions(-DWAYPT2_CORE)
endif()

find_package(ament_cmake REQUIRED)

add_subdirectory(osdk-core)
if (${CMAKE_SYSTEM_NAME} MATCHES Linux)

  set(SAMPLE_INSTALL 1)
  add_subdirectory(sample/platform/linux)

  INSTALL(DIRECTORY sample/platform/linux/
	  DESTINATION ${CMAKE_INSTALL_PREFIX}/include
	  FILES_MATCHING PATTERN "*.h*")

  INSTALL(DIRECTORY sample/platform/linux/osal
	  DESTINATION ${CMAKE_INSTALL_PREFIX}/share/src
	  FILES_MATCHING PATTERN "*.c*")

  INSTALL(DIRECTORY sample/platform/linux/hal
	  DESTINATION ${CMAKE_INSTALL_PREFIX}/share/src
	  FILES_MATCHING PATTERN "*.c*")

  INSTALL(DIRECTORY sample/platform/linux/common
	  DESTINATION ${CMAKE_INSTALL_PREFIX}/share/src
	  FILES_MATCHING PATTERN "*.c*")
endif()

#[[
INSTALL(DIRECTORY osdk-lib/${ARCH}/osdk-core/inc/
	DESTINATION ${CMAKE_INSTALL_PREFIX}/include
	FILES_MATCHING PATTERN "*.h*")

INSTALL(DIRECTORY osdk-lib/${ARCH}/advanced-sensing-2.0.3/inc/
	DESTINATION ${CMAKE_INSTALL_PREFIX}/include
	FILES_MATCHING PATTERN "*.h*")

INSTALL(DIRECTORY osdk-lib/${ARCH}/waypointv2-core-1.0.0/inc/
	DESTINATION ${CMAKE_INSTALL_PREFIX}/include
	FILES_MATCHING PATTERN "*.h*")

INSTALL(DIRECTORY osdk-lib/${ARCH}/osdk-core/lib/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
        FILES_MATCHING PATTERN "*.a*")

INSTALL(DIRECTORY osdk-lib/${ARCH}/advanced-sensing-2.0.3/lib/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
        FILES_MATCHING PATTERN "*.a*")

INSTALL(DIRECTORY osdk-lib/${ARCH}/waypointv2-core-1.0.0/lib/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
        FILES_MATCHING PATTERN "*.a*")
]]


if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
