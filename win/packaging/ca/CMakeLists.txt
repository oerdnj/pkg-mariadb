# Copyright 2010, Oracle and/or its affiliates. All rights reserved.
# 
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; version 2 of the License.
# 
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# 
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA

INCLUDE_DIRECTORIES(${WIX_DIR}/../SDK/inc)
LINK_DIRECTORIES(${WIX_DIR}/../SDK/lib)

SET(WIXCA_SOURCES CustomAction.cpp CustomAction.def)

INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR}/sql)

IF(CMAKE_SIZEOF_VOID_P EQUAL 8)
  SET(WIX_ARCH_SUFFIX "_x64")
  SET(WIX36_ARCH_SUFFIX "x64")
ELSE()
  SET(WIX_ARCH_SUFFIX)
  SET(WIX36_ARCH_SUFFIX "x86")
ENDIF()

IF(MSVC_VERSION EQUAL 1400)
  SET(WIX35_MSVC_SUFFIX "_2005")
ELSEIF(MSVC_VERSION EQUAL 1500)
  SET(WIX35_MSVC_SUFFIX "_2008")
  SET(WIX36_MSVC_SUFFIX "VS2008")
ELSEIF(MSVC_VERSION EQUAL 1600 OR MSVC_VERSION EQUAL 1700 )
  SET(WIX35_MSVC_SUFFIX "_2010")
  SET(WIX36_MSVC_SUFFIX "VS2010")
ELSE()
  # When next VS is out, add the correct version here
  MESSAGE(FATAL_ERROR "Unknown VS version")
ENDIF()
INCLUDE_DIRECTORIES(${WIX_DIR}/../SDK/${WIX36_MSVC_SUFFIX}/inc)

FIND_LIBRARY(WIX_WCAUTIL_LIBRARY 
  NAMES wcautil${WIX_ARCH_SUFFIX}  wcautil${WIX35_MSVC_SUFFIX}${WIX_ARCH_SUFFIX}
  wcautil
  PATHS 
  ${WIX_DIR}/../SDK/lib 
  ${WIX_DIR}/../SDK/${WIX36_MSVC_SUFFIX}/lib/${WIX36_ARCH_SUFFIX})

FIND_LIBRARY(WIX_DUTIL_LIBRARY 
  NAMES dutil${WIX_ARCH_SUFFIX}  dutil${WIX35_MSVC_SUFFIX}${WIX_ARCH_SUFFIX}
  dutil
  PATHS 
  ${WIX_DIR}/../SDK/lib
  ${WIX_DIR}/../SDK/${WIX36_MSVC_SUFFIX}/lib/${WIX36_ARCH_SUFFIX}
  )

ADD_VERSION_INFO(wixca SHARED WIXCA_SOURCES)
ADD_LIBRARY(wixca SHARED EXCLUDE_FROM_ALL ${WIXCA_SOURCES})
TARGET_LINK_LIBRARIES(wixca ${WIX_WCAUTIL_LIBRARY} ${WIX_DUTIL_LIBRARY} 
  msi version winservice)
