# Copyright (C) 2008-2017 TrinityCore <http://www.trinitycore.org/>
#
# This file is free software; as a special exception the author gives
# unlimited permission to copy and/or distribute it, with or without
# modifications, as long as this notice is preserved.
#
# This program is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY, to the extent permitted by law; without even the
# implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

  # We need to generate the jemalloc_def.h header based on platform-specific settings
  if (PLATFORM EQUAL 32)
    set(JEM_SIZEDEF 2)
    set(JEM_TLSMODEL)
	set(JEM_VADDRBITS 32)
  else()
    set(JEM_SIZEDEF 3)
    set(JEM_TLSMODEL "__attribute__\(\(tls_model\(\"initial-exec\"\)\)\)")
	set(JEM_VADDRBITS 48)
  endif()

  # Create the header, so we can use it
  configure_file(
    "${CMAKE_SOURCE_DIR}/modules/worldengine/deps/jemalloc/jemalloc_internal_defs.h.in.cmake"
    "${BUILDDIR}/jemalloc_internal_defs.h"
    @ONLY
  )

  # Done, let's continue
  set(jemalloc_STAT_SRC
    ${CMAKE_CURRENT_SOURCE_DIR}/src/arena.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/background_thread.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/base.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/bitmap.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/ckh.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/ctl.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/extent.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/extent_dss.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/extent_mmap.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/hash.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/hooks.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/jemalloc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/jemalloc_cpp.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/large.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/malloc_io.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/mutex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/mutex_pool.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/nstime.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/pages.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/prng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/prof.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/rtree.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/spin.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/stats.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/sz.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/tcache.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/ticker.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/tsd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/witness.c
  )

include_directories(
  ${BUILDDIR}/
  ${CMAKE_CURRENT_SOURCE_DIR}/include
)

add_definitions(-D_GNU_SOURCE -D_REENTRANT)

add_library(jemalloc STATIC ${jemalloc_STAT_SRC})

target_link_libraries(jemalloc
    PUBLIC
	  ${CMAKE_DL_LIBS})
      
set_target_properties(jemalloc
    PROPERTIES
      FOLDER
        "deps")
