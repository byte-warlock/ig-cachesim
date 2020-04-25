# Copyright (c) 2017, Insomniac Games
#
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

find_package(Qt5 COMPONENTS Widgets REQUIRED)

set(CMAKE_AUTOMOC TRUE)
set(CMAKE_AUTORCC TRUE)
set(CMAKE_AUTOUIC TRUE)

add_executable(CacheSimUI WIN32
        CacheSimMainWindow.h
        CacheSimMainWindow.h
        SymbolResolver.h
        TraceTab.h
        TraceData.h
        FlatModel.h
        TreeModel.h
        FlatProfileView.h
        TreeProfileView.h
        BaseProfileView.h

        CacheSimMainWindow.ui
        TraceTab.ui
        FlatProfileView.ui
        TreeProfileView.ui

        AnnotationView.cpp AnnotationView.h
        BaseProfileView.cpp BaseProfileView.h
        CacheSimGUIMain.cpp
        CacheSimMainWindow.cpp CacheSimMainWindow.h
        FlatModel.cpp FlatModel.h
        FlatProfileView.cpp FlatProfileView.h
        NumberFormatters.cpp NumberFormatters.h
        ObjectStack.cpp ObjectStack.h
        Precompiled.cpp Precompiled.h
        TraceData.cpp TraceData.h
        TraceTab.cpp TraceTab.h
        TreeModel.cpp TreeModel.h
        TreeProfileView.cpp TreeProfileView.h

        $<$<CXX_COMPILER_ID:MSVC>: SymbolResolverWindows.cpp>
        $<$<CXX_COMPILER_ID:GNU>: SymbolResolverLinux.cpp>

        Resources.qrc)

target_compile_definitions(CacheSimUI
        PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>: "IG_CACHESIM_API=__declspec(dllimport)" "NOMINMAX" "WIN32_LEAN_AND_MEAN" >)

target_compile_options(CacheSimUI
        PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>: /W4 /wd4127 /wd4458 /wd4200 /wd4718>)

target_link_libraries(CacheSimUI PRIVATE CacheSim Qt5::Widgets)

if (WIN32)
    target_link_libraries(CacheSimUI PRIVATE "dbghelp")

    # Post build step to deploy Qt dependencies
    find_program(WINDEPLOYQT_EXECUTABLE NAMES windeployqt HINTS ${QTDIR} ENV QTDIR PATH_SUFFIXES bin)
    add_custom_command(TARGET CacheSimUI POST_BUILD
            COMMAND ${WINDEPLOYQT_EXECUTABLE} $<TARGET_FILE:CacheSimUI>)
endif ()
