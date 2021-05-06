add_executable(simplify simplify.cc)

target_link_libraries(simplify PUBLIC mve::mve_mve mve::mve_util smvs)