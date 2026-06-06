add_library(formatter STATIC formatter.cpp)

target_include_directories(formatter PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
)

install(TARGETS formatter
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(FILES formatter.h DESTINATION include)
