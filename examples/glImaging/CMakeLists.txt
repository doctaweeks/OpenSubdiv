#
#   Copyright 2015 Pixar
#
#   Licensed under the Apache License, Version 2.0 (the "Apache License")
#   with the following modification; you may not use this file except in
#   compliance with the Apache License and the following modification to it:
#   Section 6. Trademarks. is deleted and replaced with:
#
#   6. Trademarks. This License does not grant permission to use the trade
#      names, trademarks, service marks, or product names of the Licensor
#      and its affiliates, except as required to comply with Section 4(c) of
#      the License and to reproduce the content of the NOTICE file.
#
#   You may obtain a copy of the Apache License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the Apache License with the above modification is
#   distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
#   KIND, either express or implied. See the Apache License for the specific
#   language governing permissions and limitations under the Apache License.
#

include_directories(
    "${OPENSUBDIV_INCLUDE_DIR}"
    "${GLFW_INCLUDE_DIR}"
)

set(SHADER_FILES
     shader.glsl
)

set(SOURCE_FILES
    glImaging.cpp
)

set(PLATFORM_LIBRARIES
    "${OSD_LINK_TARGET}"
    "${OPENGL_LIBRARY}"
    "${GLFW_LIBRARIES}"
)

if ( GLEW_FOUND )
    include_directories("${GLEW_INCLUDE_DIR}")
    list(APPEND PLATFORM_LIBRARIES "${GLEW_LIBRARY}")
endif()

if ( OPENCL_FOUND )
    list(APPEND PLATFORM_LIBRARIES
        "${OPENCL_LIBRARIES}"
    )
    include_directories( "${OPENCL_INCLUDE_DIRS}" )
endif()

_stringify("${SHADER_FILES}" INC_FILES)

include_directories("${CMAKE_CURRENT_BINARY_DIR}")

_add_glfw_executable(glImaging "examples"
    "${SOURCE_FILES}"
    "${SHADER_FILES}"
    "${INC_FILES}"
    $<TARGET_OBJECTS:regression_common_obj>
    $<TARGET_OBJECTS:examples_common_obj>
)

target_link_libraries(glImaging
    ${PLATFORM_LIBRARIES}
)

install(TARGETS glImaging DESTINATION "${CMAKE_BINDIR_BASE}")

if (NOT NO_GLTESTS)
    add_test(glImaging ${EXECUTABLE_OUTPUT_PATH}/glImaging -w test -l 3 -s 256 256 -a)
endif()

