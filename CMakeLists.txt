project(dragonc)
cmake_minimum_required(VERSION 2.6)



find_program(LLVM_CONFIG NAMES  llvm-config llvm-config-3.1 /usr/bin)


if(NOT EXISTS ${LLVM_CONFIG})
        message(FATAL_ERROR ${LLVM_CONFIG})
endif()

execute_process(COMMAND ${LLVM_CONFIG} --cppflags
                OUTPUT_VARIABLE LLVM_FLAGS)

execute_process(COMMAND ${LLVM_CONFIG} --ldflags
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE LLVM_LD_FLAGS)
                
execute_process(COMMAND ${LLVM_CONFIG} --libs engine jit
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE LLVM_EXECENGINE_LD_FLAGS)

execute_process(COMMAND ${LLVM_CONFIG} --libs 
                OUTPUT_STRIP_TRAILING_WHITESPACE      
                OUTPUT_VARIABLE LLVM_LIBS)

set(CMAKE_C_COMPILER clang)
set(CMAKE_CXX_COMPILER clang++)
set(CMAKE_LINKER llvm-link)
add_definitions("${LLVM_FLAGS}")


set(CMAKE_BUILD_TYPE Debug CACHE STRING "default to debug" FORCE)

aux_source_directory("${PROJECT_SOURCE_DIR}/src/." SRC_LIST)
file(GLOB INCLUDE_LIST "${PROJECT_SOURCE_DIR}/include/*.h")


include_directories("${PROJECT_SOURCE_DIR}/include/")

add_executable(${PROJECT_NAME} ${SRC_LIST} ${INCLUDE_LIST})
target_link_libraries(${PROJECT_NAME} ${LLVM_LD_FLAGS} ${LLVM_LIBS} ${LLVM_EXECENGINE_LD_FLAGS} -lpthread -ldl)
