# Copyright (C) 2013 Stephen Chandler Paul
#
# This file is free software: you may copy it, redistribute it and/or modify it
# under the terms of the GNU General Public License as published by the Free
# Software Foundation, either version 2 of this License or (at your option) any
# later version.
#
# This file is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR
# A PARTICULAR PURPOSE. See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along with
# this program. If not, see <http://www.gnu.org/licenses/>.

cmake_minimum_required(VERSION 2.6)
project(SquirrelChat C)

find_package(PkgConfig REQUIRED)
pkg_check_modules(GTK3 REQUIRED gtk+-3.0)

option (WITH_SSL            "Enable SSL support" ON)
option (GNUTLS_DEBUG_LEVEL  "Set the debug level for GnuTLS" 0)
option (CAFILE_PATH         "Set the path for the list of trusted CAs")

set(CAFILE_PATH "/etc/ssl/certs/ca-certificates.crt")

if (WITH_SSL)
    find_package(GnuTLS REQUIRED)
endif()

configure_file(src/config.h.cmake src/config.h)

add_definitions("-imacros ${CMAKE_SOURCE_DIR}/src/attributes.h
                 -D_POSIX_C_SOURCE=200809L -Wno-int-to-pointer-cast
                 -Wno-pointer-to-int-cast
                 -include ${CMAKE_CURRENT_BINARY_DIR}/src/config.h")

# C11 is only partially supported in gcc versions 4.3 and up, and fully
# supported in versions 4.8 and up. So we need to account for this.
if(CMAKE_COMPILER_IS_GNUCC)
    execute_process(COMMAND ${CMAKE_C_COMPILER} -dumpversion
                    OUTPUT_VARIABLE GCC_VERSION)
    if (GCC_VERSION VERSION_LESS 4.3)
        message(FATAL_ERROR "gcc version 4.3 or greater is required")
    elseif(GCC_VERSION VERSION_LESS 4.7)
        add_definitions("-std=c1x")
    else()
        add_definitions("-std=c11")
    endif()
else()
    add_definitions("-std=c11")
endif()

add_subdirectory(src)

# vim: expandtab:tabstop=4:shiftwidth=4:softtabstop=4:tw=80
