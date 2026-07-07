cmake_minimum_required(VERSION 3.15)
project(BlackHoleSim CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find packages with vcpkg
find_package(glfw3 CONFIG REQUIRED)
find_package(glew CONFIG REQUIRED)
find_package(glm CONFIG REQUIRED)
find_package(OpenGL REQUIRED)

# Target: 3D simulation (with Compute Shader)
add_executable(BlackHole3D src/black_hole.cpp)

target_link_libraries(BlackHole3D PRIVATE
    glfw
    GLEW::GLEW
    glm::glm
    OpenGL::GL
)

# Post-build copy shaders to executable folder
add_custom_command(TARGET BlackHole3D POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${CMAKE_CURRENT_SOURCE_DIR}/shaders/geodesic.comp"
    $<TARGET_FILE_DIR:BlackHole3D>
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${CMAKE_CURRENT_SOURCE_DIR}/shaders/grid.frag"
    $<TARGET_FILE_DIR:BlackHole3D>
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${CMAKE_CURRENT_SOURCE_DIR}/shaders/grid.vert"
    $<TARGET_FILE_DIR:BlackHole3D>
)