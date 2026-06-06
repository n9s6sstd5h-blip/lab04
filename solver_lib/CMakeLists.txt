add_library(solver_lib STATIC solver.cpp)

target_include_directories(solver_lib PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
)

install(TARGETS solver_lib
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(FILES solver.h DESTINATION include)
