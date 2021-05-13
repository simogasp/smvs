set(SMVS_SOURCES bicubic_patch.cc
        correspondence.cc
        delaunay_2d.cc
        depth_optimizer.cc
        depth_triangulator.cc
        gauss_newton_step.cc
        global_lighting.cc
        light_optimizer.cc
        mesh_generator.cc
        mesh_simplifier.cc
        sgm_stereo.cc
        sse_vector.cc
        stereo_view.cc
        surface.cc
        surface_derivative.cc
        surface_patch.cc
        view_selection.cc
        )

set(SMVS_HEADERS bicubic_patch.h
        block_sparse_matrix.h
        conjugate_gradient.h
        correspondence.h
        defines.h
        delaunay_2d.h
        depth_optimizer.h
        depth_triangulator.h
        gauss_newton_step.h
        global_lighting.h
        ldl_decomposition.h
        light_optimizer.h
        mesh_generator.h
        mesh_simplifier.h
        quad_edge.h
        sgm_stereo.h
        spherical_harmonics.h
        sse_vector.h
        stereo_view.h
        surface_derivative.h
        surface.h
        surface_patch.h
        thread_pool.h
        view_selection.h
        )
add_library(smvs ${SMVS_SOURCES})

target_include_directories(smvs PUBLIC
        "$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/lib>"
        "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>/smvs")

set_target_properties(smvs PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(smvs PROPERTIES DEBUG_POSTFIX "d")

if(NOT MSVC)
    target_compile_options(smvs PUBLIC -msse2 -msse3 -mpopcnt)
else()
    # to use std::min and max
    target_compile_definitions(smvs PUBLIC NOMINMAX)
endif()

target_link_libraries(smvs PUBLIC mve::mve_mve mve::mve_math mve::mve_util PRIVATE PNG::PNG JPEG::JPEG TIFF::TIFF Threads::Threads)

# Targets:
#   * <prefix>/lib/libmve_sfm.a
#   * header location after install: <prefix>/include/*/*.h
install(
        TARGETS smvs
        EXPORT ${targets_export_name}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_BINDIR}
        INCLUDES DESTINATION "${include_install_dir}"
)

# Headers:
install(
        FILES ${SMVS_HEADERS}
        DESTINATION "${include_install_dir}/smvs"
)
