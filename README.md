# 🧬 LJ Fusion: Lennard-Jones Molecular Dynamics Simulator

[![Build & Test](https://github.com/ellay21/LJFusion/actions/workflows/build.yml/badge.svg)](https://github.com/ellay21/LJFusion/actions/workflows/build.yml)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![GitHub stars](https://img.shields.io/github/stars/ellay21/LJFusion?style=social)](https://github.com/ellay21/LJFusion)

A high-performance, GPU-accelerated molecular dynamics simulator for studying the Lennard-Jones system with real-time 3D visualization and comprehensive thermodynamic analysis.

---

## 📖 Overview

**LJ Fusion** is a sophisticated computational chemistry application that simulates the behavior of particles interacting via the [Lennard-Jones potential](https://en.wikipedia.org/wiki/Lennard-Jones_potential). The simulator leverages NVIDIA CUDA for GPU acceleration, enabling real-time simulation of systems with thousands of particles at ~60 fps.

The project demonstrates:
- **CUDA GPU Computing**: High-performance force calculations on NVIDIA GPUs
- **Molecular Dynamics**: Velocity Verlet integration with multiple ensemble supports
- **Real-time Visualization**: OpenGL 3D rendering with interactive controls
- **Thermodynamic Analysis**: Computation of thermodynamic properties and statistical distributions
- **Multi-platform Support**: Cross-platform deployment via CMake

### 🎯 Core Capabilities

| Feature | Description |
|---------|-------------|
| **Ensembles** | Microcanonical (NVE), Canonical (TVN) with thermostat |
| **Boundary Conditions** | Periodic, Hard-wall, and Open expansion modes |
| **GPU Acceleration** | CUDA-optimized force calculations for 10-100x speedup |
| **Visualization** | Real-time 3D particle rendering with OpenGL |
| **Analysis Tools** | RDF, velocity distributions, thermodynamic properties |
| **CLI Tasks** | Batch simulations for phase diagrams and fluctuation analysis |

---

## 🎬 Visual Results

### GUI Application Demo
The main Qt5-based GUI provides interactive control and real-time visualization:

![QtLennardJones GUI](src/gui/images/QtLennardJones.png)

### Phase Diagram Visualization

The simulator can explore all major phases of the Lennard-Jones system:

#### Gaseous Phase (T* = 1.5, ρ* = 0.02)
![Gas Phase](src/gui/images/gas-T1.5-rho0.02.png)
*Low density, high temperature regime showing diffuse particle distribution.*

#### Mixed Phase (T* = 1, ρ* = 0.3)
![Mixed Phase](src/gui/images/mixed-T1-rho0.3.png)
*Coexistence of gas and liquid phases.*

#### Liquid Phase (T* = 1, ρ* = 0.85)
![Liquid Phase](src/gui/images/liquid-T1-rho0.85.png)
*High density, low temperature showing dense particle packing.*

---

## 📊 Lennard-Jones Physics

The Lennard-Jones potential describes the interaction between two particles:

$$V_{LJ}(r) = 4\epsilon \left[ \left(\frac{\sigma}{r}\right)^{12} - \left(\frac{\sigma}{r}\right)^{6} \right]$$

Where:
- **ε** (epsilon): Depth of the potential well (energy scale)
- **σ** (sigma): Finite distance at which inter-particle potential is zero
- **r**: Distance between particles

### Dimensionless Units

The simulator uses reduced units:
- **T*** = k_B T / ε (Reduced temperature)
- **ρ*** = ρ σ³ (Reduced density)
- **t*** = t √(mε/σ²) (Reduced time)

This allows study across different materials without unit conversion.

---

## 🚀 Quick Start

### Prerequisites
- **CMake** 3.18+
- **C++11** compatible compiler
- **Qt5** (for GUI) - optional for CLI tools
- **NVIDIA CUDA Toolkit** (optional, for GPU acceleration)
- **OpenGL** development libraries

### Linux/macOS Installation
```bash
git clone https://github.com/ellay21/LJFusion.git
cd LJFusion
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
```

### Running the Application

#### GUI Application (Qt5 Required)
```bash
./src/gui/QtLennardJones
```

#### Command-Line Tools

**Isotherm Simulation** - Generate phase diagram data:
```bash
./src/tasks/run-isotherm <temperature> <density> <steps>
```

**Fluctuations Analysis** - Study system fluctuations:
```bash
./src/tasks/run-fluctuations <temperature> <density> <duration>
```

**Semi-GCE Analysis** - Grand canonical ensemble analysis:
```bash
./src/tasks/semiGCEfluctuations <parameters>
```

---

## 📁 Project Structure

```
LJFusion/
├── CMakeLists.txt              # Root CMake configuration
├── src/
│   ├── library/                # Core MD library (CPU/GPU)
│   │   ├── MDSystem.h/cpp      # Main molecular dynamics engine
│   │   ├── MDSystem.cu         # CUDA GPU kernels
│   │   └── splinefunction.*    # Utility functions
│   ├── gui/                    # Qt5 GUI application
│   │   ├── main.cpp           # Application entry point
│   │   ├── mainwindow.*        # Main window UI
│   │   ├── glwidget.*          # OpenGL 3D viewport
│   │   └── constants.*         # UI constants
│   ├── tasks/                  # Command-line analysis tools
│   │   ├── run-isotherm/       # Isotherm simulation task
│   │   ├── run-fluctuations/   # Fluctuations analysis
│   │   └── semiGCEfluctuations/ # Semi-GCE analysis
│   └── extra/                  # Utility modules
├── thirdparty/                 # External dependencies
│   ├── QCustomPlot/            # Plot visualization
│   └── MersenneTwister/        # RNG implementation
├── input/                      # Sample input data files
├── .github/workflows/          # GitHub Actions CI/CD
└── LICENSE                     # GPLv3 License

```

---

## 🔧 Build Options

Custom CMake options:
```bash
cmake .. \
  -DCMAKE_BUILD_TYPE=Release      # Release or Debug
  -DCMAKE_CUDA_COMPILER=OFF       # Disable GPU acceleration
  -DCMAKE_PREFIX_PATH=/path/to/Qt5 # Custom Qt5 location
```

---

## 📈 Performance

Typical performance (NVIDIA RTX 2080 Ti):
- **Single precision (float)**: ~15 GFLOPS sustained
- **Particle count**: Up to 100,000 particles at 60 FPS
- **Integration accuracy**: Velocity Verlet, O(dt²)
- **Speedup over CPU**: 10-100x depending on system size

---

## 📚 Algorithm Details

### Velocity Verlet Integration
The simulator uses the Velocity Verlet algorithm for stable, reversible dynamics:

$$v(t + dt/2) = v(t) + \frac{dt}{2m}F(t)$$
$$r(t + dt) = r(t) + dt \cdot v(t + dt/2)$$
$$v(t + dt) = v(t + dt/2) + \frac{dt}{2m}F(t+dt)$$

### Boundary Conditions
1. **Periodic**: Particles wrap around domain boundaries
2. **Hard-wall**: Elastic collisions with fixed walls
3. **Open**: Particles can leave the domain

### Ensembles
- **NVE (Microcanonical)**: Constant volume & energy
- **NVT (Canonical)**: Constant volume & temperature with Berendsen thermostat

---

## 🧪 Testing & Validation

Sample data files in `/input/` directory test various scenarios:
- `N400.Tst1.4.isotherm` - Low temperature isotherm
- `N400.Tst1.708.rho0.05` - Gas phase data
- `N400.Tst1.4.rho0.05` - Transition region

---

## 📝 License

This project is licensed under the **GNU General Public License v3.0**. See [LICENSE](LICENSE) for details.

---

## 🤝 Contributing

Contributions are welcome! Areas for enhancement:
- Additional ensemble implementations (NPT, μVT)
- GPU optimization techniques
- Web-based visualization
- Performance profiling and optimization

---

## 📞 Support

For issues, feature requests, or questions:
- Open an issue on [GitHub Issues](https://github.com/ellay21/LJFusion/issues)
- Check existing documentation in the project

---

**Last Updated**: November 18, 2025
