# CMakeLists.txt
#
# $Id: CMakeLists.txt 31995 2010-02-24 22:32:10Z jmayer $
#
# Wireshark - Network traffic analyzer
# By Gerald Combs <gerald@wireshark.org>
# Copyright 1998 Gerald Combs
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either version 2
# of the License, or (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
#

project(ambit-wireshark-plugin C)

cmake_minimum_required(VERSION 2.6)
set(CMAKE_BACKWARDS_COMPATIBILITY 2.6)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
set(CMAKE_INSTALL_LIBDIR ~/.wireshark)

INCLUDE(UseMakeDissectorReg)
  
set(GLIB2_MIN_VERSION 2.4.0)

find_package(GLIB2)
include_directories (${GLIB2_INCLUDE_DIRS})

find_package(Wireshark)
include_directories (${WIRESHARK_INCLUDE_DIRS})

set(LINK_MODE_LIB SHARED)
set(LINK_MODE_MODULE MODULE)


set(DISSECTOR_SRC
	ambit-dissector.c
)

set(PLUGIN_FILES
	plugin.c
	${DISSECTOR_SRC}
)

set(CLEAN_FILES
	${PLUGIN_FILES}
)

if (WERROR)
	set_source_files_properties(
		${CLEAN_FILES}
		PROPERTIES
		COMPILE_FLAGS -Werror
	)
endif()

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

register_dissector_files(plugin.c
	plugin
	${DISSECTOR_SRC}
)

add_library(ws-ambit ${LINK_MODE_MODULE}
	${PLUGIN_FILES}
)
set_target_properties(ws-ambit PROPERTIES OUTPUT_NAME "ambit")
set_target_properties(ws-ambit PROPERTIES PREFIX "")
set_target_properties(ws-ambit PROPERTIES LINK_FLAGS "${WS_LINK_FLAGS}")

target_link_libraries(ws-ambit ${WIRESHARK_LIBRARIES})

install(TARGETS ws-ambit
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/plugins NAMELINK_SKIP
)

