# [NEVER REMOVE THIS LINE] WARNING: this file is generated, please edit kendryte-package.json file instead.
##### include(reset) #####
### reset basic var
set(CMAKE_C_COMPILER_WORKS 1)
set(CMAKE_CXX_COMPILER_WORKS 1)

# set(CMAKE_SYSTEM "Generic")
set(CMAKE_SYSTEM_NAME "Generic")

# set(CMAKE_HOST_SYSTEM "Generic")
# set(CMAKE_HOST_SYSTEM_NAME "Generic")
#
set(CMAKE_CROSSCOMPILING TRUE)
# set(CMAKE_C_COMPILER_FORCED TRUE)
# set(CMAKE_CXX_COMPILER_FORCED TRUE)
# set(CMAKE_ASM_COMPILER_FORCED TRUE)
#

set(SOURCE_FILES "")
set(LDFLAGS "")
set(CMAKE_EXE_LINKER_FLAGS "")
set(CMAKE_SHARED_LINKER_FLAGS "")
set(CMAKE_MODULE_LINKER_FLAGS "")


cmake_minimum_required(VERSION 3.0.0)
set(PROJECT_NAME "helius_microrl")
##### include(macros) #####
macro(header_directories parent)
    file(GLOB_RECURSE newList ${parent}/*.h)
    set(dir_list "")
    foreach (file_path ${newList})
        get_filename_component(dir_path ${file_path} DIRECTORY)
        set(dir_list ${dir_list} ${dir_path})
    endforeach ()
    list(REMOVE_DUPLICATES dir_list)

    include_directories(${dir_list})
endmacro()

macro(add_source_files)
    file(GLOB_RECURSE newlist ${ARGN})

    foreach (filepath ${newlist})
        string(FIND ${filepath} ${CMAKE_BINARY_DIR} found)
        if (NOT found EQUAL 0)
			list(APPEND SOURCE_FILES ${filepath})
        endif ()
    endforeach ()
    set("SOURCE_FILES_${PROJECT_NAME}" "${SOURCE_FILES}" CACHE STRING "Dump files to compile for '${PROJECT_NAME}'. Not used by program.")
endmacro()

function(JOIN VALUES GLUE OUTPUT)
    string(REGEX REPLACE "([^\\]|^);" "\\1${GLUE}" _TMP_STR "${VALUES}")
    string(REGEX REPLACE "[\\](.)" "\\1" _TMP_STR "${_TMP_STR}") #fixes escaping
    set(${OUTPUT} "${_TMP_STR}" PARENT_SCOPE)
endfunction()

function(removeDuplicateSubstring stringIn stringOut)
    separate_arguments(stringIn)
    list(REMOVE_DUPLICATES stringIn)
    string(REPLACE ";" " " stringIn "${stringIn}")
    set(${stringOut} "${stringIn}" PARENT_SCOPE)
endfunction()

macro(add_compile_flags WHERE)
    JOIN("${ARGN}" " " STRING_ARGS)
    if (${WHERE} STREQUAL C)
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${STRING_ARGS}")

    elseif (${WHERE} STREQUAL CXX)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${STRING_ARGS}")

    elseif (${WHERE} STREQUAL LD)
        set(LDFLAGS "${LDFLAGS} ${STRING_ARGS}")
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${STRING_ARGS}")
        set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} ${STRING_ARGS}")
        set(CMAKE_MODULE_LINKER_FLAGS "${CMAKE_MODULE_LINKER_FLAGS} ${STRING_ARGS}")

    elseif (${WHERE} STREQUAL BOTH)
        add_compile_flags(C ${ARGN})
        add_compile_flags(CXX ${ARGN})

    elseif (${WHERE} STREQUAL ALL)
        add_compile_flags(C ${ARGN})
        add_compile_flags(CXX ${ARGN})
        add_compile_flags(LD ${ARGN})

    else ()
        message(FATAL_ERROR "add_compile_flags - only support: C, CXX, BOTH, LD, ALL")
    endif ()
endmacro()


##### include(ideSettings) #####
# set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
else()
    if((NOT CMAKE_BUILD_TYPE STREQUAL "Debug") AND (NOT CMAKE_BUILD_TYPE STREQUAL "Release"))
        message(FATAL_ERROR "CMAKE_BUILD_TYPE must either be Debug or Release instead of ${CMAKE_BUILD_TYPE}")
    endif()
endif()
set(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE STRING "Build type options are: Debug Release RelWithDebInfo MinSizeRel")

## - Debug & Release
if(CMAKE_BUILD_TYPE STREQUAL Debug)
    add_definitions(-DDEBUG=1)
endif()

## definitions in macros
add_definitions(-DCONFIG_LOG_LEVEL=LOG_INFO -DCONFIG_LOG_ENABLE -D__riscv64)
if(DEFINED KENDRYTE_IDE)
	set(CMAKE_COLOR_MAKEFILE FALSE)
else()
	add_definitions(-DCONFIG_LOG_COLORS)
endif()

set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE CACHE INTERNAL "must set to true, never edit or IDEs will not work")


##### include(toolchain) #####
if (WIN32)
    set(BINARY_EXTENSION ".exe")
else ()
    set(BINARY_EXTENSION "")
endif ()

set(CMAKE_C_COMPILER "gcc")
set(CMAKE_CXX_COMPILER "g++")
set(CMAKE_LINKER "ld")
set(CMAKE_AR "ar")
set(CMAKE_OBJCOPY "objcopy")
set(CMAKE_SIZE "size")
set(CMAKE_OBJDUMP "objdump")
set(CMAKE_RANLIB "ranlib")
set(CMAKE_NM "nm")
set(CMAKE_STRIP "strip")
set(CMAKE_C_COMPILER_AR "gcc-ar")
set(CMAKE_C_COMPILER_RANLIB "gcc-ranlib")
set(CMAKE_CXX_COMPILER_AR "gcc-ar")
set(CMAKE_CXX_COMPILER_RANLIB "gcc-ranlib")

set(CMAKE_TOOLCHAIN_PREFIX "riscv64-unknown-elf-" CACHE INTERNAL "this const is defined by toolchain")
set(CMAKE_EXECUTABLE_FORMAT "ELF" CACHE INTERNAL "this const is defined by toolchain")

foreach(CMD CMAKE_C_COMPILER CMAKE_CXX_COMPILER CMAKE_LINKER CMAKE_AR CMAKE_OBJCOPY CMAKE_SIZE CMAKE_OBJDUMP CMAKE_RANLIB CMAKE_NM CMAKE_STRIP CMAKE_C_COMPILER_AR CMAKE_C_COMPILER_RANLIB CMAKE_CXX_COMPILER_AR CMAKE_CXX_COMPILER_RANLIB)
	set(${CMD} "${CMAKE_TOOLCHAIN_PREFIX}${${CMD}}${BINARY_EXTENSION}" CACHE FILEPATH "the path of ${CMD}" FORCE)
endforeach()

#execute_process(COMMAND ${CMAKE_C_COMPILER} -print-file-name=crtbegin.o OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE CRTBEGIN_OBJ)
#execute_process(COMMAND ${CMAKE_C_COMPILER} -print-file-name=crtend.o OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE CRTEND_OBJ)
#execute_process(COMMAND ${CMAKE_C_COMPILER} -print-file-name=crti.o OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE CRTI_OBJ)
#execute_process(COMMAND ${CMAKE_C_COMPILER} -print-file-name=crtn.o OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE CRTN_OBJ)
#
#set(CMAKE_C_LINK_EXECUTABLE
#       "<CMAKE_C_COMPILER>  <FLAGS> <CMAKE_C_LINK_FLAGS> <LINK_FLAGS> \"${CRTI_OBJ}\" \"${CRTBEGIN_OBJ}\" <OBJECTS> \"${CRTEND_OBJ}\" \"${CRTN_OBJ}\" -o <TARGET> <LINK_LIBRARIES>"
#       CACHE STRING "CMAKE_C_LINK_EXECUTABLE"
#       )
#
#set(CMAKE_CXX_LINK_EXECUTABLE
#       "<CMAKE_CXX_COMPILER>  <FLAGS> <CMAKE_CXX_LINK_FLAGS> <LINK_FLAGS> \"${CRTI_OBJ}\" \"${CRTBEGIN_OBJ}\" <OBJECTS> \"${CRTEND_OBJ}\" \"${CRTN_OBJ}\" -o <TARGET> <LINK_LIBRARIES>"
#       CACHE STRING "CMAKE_CXX_LINK_EXECUTABLE"
#       )
#



##### flags from config json #####
##### internal flags #####
##### include(coreFlags) #####
add_compile_flags(LD -nostartfiles -Wl,--gc-sections)
add_compile_flags(BOTH -mcmodel=medany)


add_compile_flags(LD
  -T "${CMAKE_CURRENT_LIST_DIR}/kendryte_libraries/kendryte-standalone-sdk/lds/kendryte.ld"
)
##### include(fix9985) #####
### http://www.cmake.org/Bug/view.php?id=9985
string(REPLACE "-rdynamic" "" _C_FLAGS "${CMAKE_SHARED_LIBRARY_LINK_C_FLAGS}")
string(REPLACE "-rdynamic" "" _CXX_FLAGS "${CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS}")
set(CMAKE_SHARED_LIBRARY_LINK_C_FLAGS "${_C_FLAGS}")
set(CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS "${_CXX_FLAGS}")


##### Main Section #####
message("======== PROJECT ========")
project(${PROJECT_NAME})
## add include from self and dependency
include_directories(
  "${CMAKE_CURRENT_LIST_DIR}/include"
  "${CMAKE_CURRENT_LIST_DIR}/kendryte_libraries/kendryte-standalone-sdk/lib/drivers/include"
  "${CMAKE_CURRENT_LIST_DIR}/kendryte_libraries/kendryte-standalone-sdk/lib/bsp/include"
  "${CMAKE_CURRENT_LIST_DIR}/kendryte_libraries/kendryte-standalone-sdk/lib/utils/include"
)
## add source from config json
add_source_files(
  "${CMAKE_CURRENT_LIST_DIR}/config/fpioa-config.c"
  "${CMAKE_CURRENT_LIST_DIR}/config/fpioa-config.h"
)
add_source_files(
  "${CMAKE_CURRENT_LIST_DIR}/src/*.c"
  "${CMAKE_CURRENT_LIST_DIR}/src/*.h"
  "${CMAKE_CURRENT_LIST_DIR}/include/*.h"
)
add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/kendryte_libraries/kendryte-standalone-sdk" "kendryte-standalone-sdk")
## No extra list
## final create executable or library
add_library(${PROJECT_NAME} ${SOURCE_FILES})
## no properties
## set definitions
add_compile_definitions(DEFAULT_PROMPT="> ")
add_compile_definitions(DEFAULT_PROMPT_LEN=2)
target_link_libraries(${PROJECT_NAME} -Wl,--start-group gcc m c -Wl,--end-group)
## dependencies link
target_link_libraries(${PROJECT_NAME}
  -Wl,--start-group
    "kendryte-standalone-sdk"
  -Wl,--end-group
)
##### include(afterProject) #####
removeDuplicateSubstring("${CMAKE_C_FLAGS}" CMAKE_C_FLAGS)
removeDuplicateSubstring("${CMAKE_CXX_FLAGS}" CMAKE_CXX_FLAGS)


##### include(fix9985) #####
### http://www.cmake.org/Bug/view.php?id=9985
string(REPLACE "-rdynamic" "" _C_FLAGS "${CMAKE_SHARED_LIBRARY_LINK_C_FLAGS}")
string(REPLACE "-rdynamic" "" _CXX_FLAGS "${CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS}")
set(CMAKE_SHARED_LIBRARY_LINK_C_FLAGS "${_C_FLAGS}")
set(CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS "${_CXX_FLAGS}")


##### include(dumpConfig) #####
set(CMAKE_SYSTEM_NAME "Generic")

message("")
message("Project: ${PROJECT_NAME}")
message("  LIST_FILE=${CMAKE_PARENT_LIST_FILE}")
message("  TOOLCHAIN=${TOOLCHAIN}")
message("  KENDRYTE_IDE=${KENDRYTE_IDE}")
message("  BUILDING_SDK=${BUILDING_SDK}")
message("  _CMAKE_TOOLCHAIN_PREFIX=${_CMAKE_TOOLCHAIN_PREFIX}")
message("  _CMAKE_TOOLCHAIN_LOCATION=${_CMAKE_TOOLCHAIN_LOCATION}")


message("")
message("  CMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}")
message("  CMAKE_C_COMPILER=${CMAKE_C_COMPILER}")
message("  CMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}")
message("  CMAKE_LINKER=${CMAKE_LINKER}")
message("  CMAKE_OBJCOPY=${CMAKE_OBJCOPY}")
message("  CMAKE_OBJDUMP=${CMAKE_OBJDUMP}")
message("  CMAKE_MAKE_PROGRAM=${CMAKE_MAKE_PROGRAM}")

message("")
message("  CMAKE_C_FLAGS=${CMAKE_C_FLAGS}")
message("  CMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}")
message("  LDFLAGS=${LDFLAGS}")
message("  CMAKE_BINARY_DIR=${CMAKE_BINARY_DIR}")

message("Makefile created.")
message("")
message("")
