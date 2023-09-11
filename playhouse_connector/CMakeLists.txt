# Sources and Headers
# file(GLOB_RECURSE LIBRARY_INCLUDES "./include/*.h" "./src/*.h")
# file(GLOB_RECURSE LIBRARY_SOURCES "./include/*.cpp" "./src/*.cpp")
# file(GLOB_RECURSE LIBRARY_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/include/*.h")
# file(GLOB_RECURSE LIBRARY_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")

set(LIBRARY_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/my_lib.cc")
set(LIBRARY_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/include/my_lib.h")

set(LIBRARY_INCLUDES "${CMAKE_CURRENT_SOURCE_DIR}" "${CMAKE_BINARY_DIR}/configured_files/include")

# MyLib Library
add_library(${LIBRARY_NAME} STATIC ${LIBRARY_SOURCES} ${LIBRARY_HEADERS})
target_include_directories(${LIBRARY_NAME} PUBLIC ${LIBRARY_INCLUDES} )
target_link_libraries(
    ${LIBRARY_NAME}
    PRIVATE nlohmann_json::nlohmann_json
            fmt::fmt
            spdlog::spdlog
            cxxopts::cxxopts
            cppserver
            )

if(${ENABLE_WARNINGS})
    target_set_warnings(
        TARGET
        ${LIBRARY_NAME}
        ENABLE
        ${ENABLE_WARNINGS}
        AS_ERRORS
        ${ENABLE_WARNINGS_AS_ERRORS})
endif()

if(${ENABLE_LTO})
    target_enable_lto(
        TARGET
        ${LIBRARY_NAME}
        ENABLE
        ON)
endif()

if(${ENABLE_CLANG_TIDY})
    add_clang_tidy_to_target(${LIBRARY_NAME})
endif()

if(${ENABLE_TESTING})
    add_subdirectory(tests)
endif()
