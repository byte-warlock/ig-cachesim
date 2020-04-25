# Copyright (c) 2017, Insomniac Games
# All rights reserved.
# 
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
# 
# Redistributions of source code must retain the above copyright notice, this
# list of conditions and the following disclaimer.
# 
# Redistributions in binary form must reproduce the above copyright notice, this
# list of conditions and the following disclaimer in the documentation and/or
# other materials provided with the distribution.
# 
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
# ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
# DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
# FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
# SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
# CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
# OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

set(SRC_FILES
        include/CacheSim/CacheSim.h
        src/CacheSimCommon.inl
        include/CacheSim/CacheSimData.h
        src/CacheSimInternals.cpp
        include/CacheSim/CacheSimInternals.h
        src/GenericHashTable.h
        src/Md5.cpp
        src/Md5.h
        src/Platform.h
        src/Precompiled.cpp
        src/Precompiled.h
        $<$<CXX_COMPILER_ID:MSVC>: src/CacheSimWindows.cpp src/PlatformWindows.cpp>
        $<$<CXX_COMPILER_ID:GNU>: src/CacheSimLinux.cpp src/PlatformLinux.cpp>)

set_source_files_properties(src/CacheSimCommon.inl PROPERTIES HEADER_FILE_ONLY TRUE)

add_library(CacheSim SHARED ${SRC_FILES})

target_compile_definitions(CacheSim
        INTERFACE
        $<$<CXX_COMPILER_ID:MSVC>: "IG_CACHESIM_API=__declspec(dllimport)" >
        PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>: "IG_CACHESIM_API=__declspec(dllexport)" "NOMINMAX" "WIN32_LEAN_AND_MEAN" "_CRT_SECURE_NO_WARNINGS" >
        $<$<CXX_COMPILER_ID:GNU>: "IG_CACHESIM_API=__attribute__ ((visibility (\"default\")))" >)

target_compile_options(CacheSim
        PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>: /W4>
        $<$<CXX_COMPILER_ID:GNU>: -fvisibility=hidden>)

target_link_libraries(CacheSim PUBLIC udis86)

if (UNIX)
    target_link_libraries(CacheSim PUBLIC ${CMAKE_DL_LIBS})
endif ()

target_include_directories(CacheSim PUBLIC include)
