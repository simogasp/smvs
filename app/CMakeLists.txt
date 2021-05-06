find_package(mve REQUIRED)
find_package(Threads REQUIRED)

add_executable(smvsrecon smvsrecon.cc)

target_link_libraries(smvsrecon PUBLIC mve::mve_mve mve::mve_util smvs Threads::Threads)

install(TARGETS smvsrecon DESTINATION ${CMAKE_INSTALL_BINDIR})