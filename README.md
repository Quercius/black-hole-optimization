![C++17](https://img.shields.io/badge/C%2B%2B-17-blue.svg)
![OpenGL 4.3](https://img.shields.io/badge/OpenGL-4.3-brightgreen.svg)
![Physics](https://img.shields.io/badge/Physics-General__Relativity-purple.svg)

# Black Hole GPU Optimization & Raytracing

> **Note:** This repository is a performance-optimized and architecturally refactored fork of the original physics project by [kavan010](https://github.com/kavan010/black_hole). Thanks to him for his amazing work!

A physics-based simulation of a Black Hole, rendering gravitational lensing and an accretion disk in real-time. The project utilizes **GPU Compute Shaders** to perform volumetric raytracing through curved spacetime, dictated by Einstein's theory of General Relativity and the Schwarzschild metric.

> **Project Scope:** The core performance optimizations detailed below have been submitted as a Pull Request to the original repository. This specific fork now serves as my personal development branch focused on advanced visual rendering, aesthetic enhancements, and an upcoming architectural migration to Vulkan, diverging from the original N-Body gravity focus.

## The Simulation

This project uses a mathematical Backward Raytracing approach implemented in GLSL Compute Shaders:

* **The Schwarzschild Metric:** Light rays are mathematically bent by the extreme gravity of the singularity.

* **Runge-Kutta 4 (RK4) Integration:** To solve the differential equations of the geodesic paths, the engine uses 4th-order Runge-Kutta numerical integration, allowing for highly accurate orbital tracking around the Photon Sphere.

## Core Performance Optimization (~5x faster)

The initial focus of this fork was to take the original codebase and engineer it to run at peak efficiency on modern GPU architectures.

**These architectural changes resulted in a ~79% reduction in render times (~4.7x Speedup) at higher resolutions.**

### GPU Bottlenecks Resolved

* **Adaptive Step Size Integration:** Replaced the brute-force, fixed integration steps with a dynamically scaled scalar. Rays far from the gravitational well take massive leaps, while the step size clamps to microscopic values only when approaching extreme curvature. This drastically reduced the computational load per pixel.

* **Thread / Warp Divergence Mitigation:** Implemented a "Smart Early Exit" for escaping rays. By evaluating radial velocity (`dr > 0`), rays heading into deep space terminate instantly, freeing up GPU Warps and preventing SIMT execution stalls caused by rays trapped in the Photon Sphere.

### CPU Hot-Loop Optimizations

* **Memory & I/O:** Eliminated severe memory leaks by replacing heap-allocated `std::vector` inside the $O(N^2)$ hot-loop with stack-allocated `glm::dvec3`.

* **Framerate Independence:** Physics updates are properly scaled by Delta Time (`dt`).

## Project Evolution & Roadmap

While the core engine optimizations remain, the N-body gravitational interactions have been deliberately removed from this fork to focus CPU/GPU resources entirely on the optical simulation of the singularity.

This project is continually evolving. Upcoming milestones include:

* [ ] **Aesthetic Enhancements:** Implement physically-based bloom, redshift/blueshift Doppler effects, and a procedural starbox to enhance the visual fidelity of the accretion disk and background space.

* [ ] **API Migration (Vulkan):** Port the entire Compute Shader pipeline and rendering context to **Vulkan** to leverage lower-level memory management, explicit command buffers, and native Subgroup Operations to further optimize thread divergence.

## Build Instructions

### Requirements

* C++17 compatible compiler (MSVC, GCC, Clang)

* [CMake](https://cmake.org/)

* [vcpkg](https://vcpkg.io/en/) (Recommended for dependency management)

### Building with CMake & vcpkg

1. **Clone the repository:**

   ```bash
   git clone https://github.com/Quercius/black-hole-optimization.git
   cd black-hole-optimization
   ```

2. **Install dependencies:**
	Ensure **vcpkg** is installed on your system. The required packages (glfw3, glew, glm, opengl) are automatically handled via Manifest Mode (vcpkg.json).

3. **Configure the project:**
	```bash
	cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE=/path/to/your/vcpkg/scripts/buildsystems/vcpkg.cmake
	```

4. **Build the executable:**
	```bash
	cmake --build build --config Release
	```

5. **Run the simulation:** 
	Navigate to build/Release (or build/Debug) and run BlackHole3D.exe.


## Controls 
* **Left Mouse Click + Drag:** Orbit Camera
* **Scroll Wheel:** Zoom In / Out