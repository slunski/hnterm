set(TARGET hnterm)

if (EMSCRIPTEN)
    set (CMAKE_CXX_FLAGS "-s ALLOW_MEMORY_GROWTH=1 -s FETCH=1 -s ASSERTIONS=1 -s DISABLE_EXCEPTION_CATCHING=0")
endif()

option(HNTERM_ENABLE_API_CACHE "hnterm: enable API caching" OFF)

if (EMSCRIPTEN)
    add_executable(${TARGET}
        main.cpp
        hn-state.cpp
        impl-emscripten.cpp
        )

    target_include_directories(${TARGET} PRIVATE
        ..
        )

    target_link_libraries(${TARGET} PRIVATE
        imtui-core
        imtui-emscripten
        )

    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/index-tmpl.html ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${TARGET}/index.html @ONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/style.css ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${TARGET}/style.css @ONLY)
else()
    add_executable(${TARGET}
        main.cpp
        hn-state.cpp
        impl-ncurses.cpp
        )

    target_include_directories(${TARGET} PRIVATE
        ..
        ${CURL_INCLUDE_DIR}
        )

    target_link_libraries(${TARGET} PRIVATE
        imtui-ncurses
        ${CURL_LIBRARIES}
        )

    install(TARGETS hnterm RUNTIME DESTINATION bin)
endif()

if (HNTERM_ENABLE_API_CACHE)
    target_compile_definitions(${TARGET} PRIVATE ENABLE_API_CACHE)
endif()
