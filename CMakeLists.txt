cmake_minimum_required(VERSION 3.11)
project(loader LANGUAGES C CXX ASM_MASM)
enable_language(ASM_MASM)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(LOUDSUNRUN_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

set(LOUDSUNRUN_INCLUDE
    ${LOUDSUNRUN_DIR}/Macros.h
    ${LOUDSUNRUN_DIR}/Structs.h
)
set(LOUDSUNRUN_SOURCE
    ${LOUDSUNRUN_DIR}/Testing.c
)

add_executable(LoudSunRun ${LOUDSUNRUN_INCLUDE} ${LOUDSUNRUN_SOURCE})
target_include_directories(LoudSunRun PRIVATE ${LOUDSUNRUN_DIR})
target_compile_definitions(LoudSunRun PRIVATE UNICODE _UNICODE)

# The MASM stub (Spoof/fixup) is x64 only, so it is built as an OBJECT library
# and only attached on x64 builds.
if(CMAKE_GENERATOR_PLATFORM STREQUAL "x64" OR CMAKE_SIZEOF_VOID_P EQUAL 8)
    add_library(loudsunrun_asm OBJECT ${LOUDSUNRUN_DIR}/test.asm)
    target_link_libraries(LoudSunRun PRIVATE $<TARGET_OBJECTS:loudsunrun_asm>)
endif()
