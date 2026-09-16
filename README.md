# DeepMimic Portable Distribution

A self-contained, portable Windows x64 distribution of DeepMimic, designed to eliminate the complex setup process required to run the original project. It includes an embedded Python environment, all necessary dependencies, and a simple launcher.

## Original Project

This distribution is based on **DeepMimic** by Xue Bin Peng. The original source code is available at [https://github.com/xbpeng/DeepMimic](https://github.com/xbpeng/DeepMimic). The code in this distribution is **unmodified** from the original repository. No changes have been made to the simulation, learning algorithms, or scenes.

## Contents

- Embedded Python 3.7 (64-bit)
- Required Python packages: TensorFlow 1.13.1, NumPy, PyOpenGL, MPI4Py, etc.
- Prebuilt `_DeepMimicCore.pyd` (compiled from the original source)
- Original `args` and `data` directories
- All necessary runtime DLLs (freeglut, GLEW, etc.)
- Launcher (`run.bat`)

## Usage

1. Extract the archive to a directory of your choice.
2. Run `run.bat`. 
3. Select a scene from the menu.
4. The simulation window will open. Use the mouse to control the camera and interact with the character.

No installation is required. The distribution is fully portable and can be run from a USB drive.

## Notes

- This distribution is intended for quick evaluation and testing of DeepMimic. It does not include training capabilities by default. Training requires Microsoft MPI and additional configuration.
- The launcher simply invokes the embedded Python interpreter with the appropriate arguments. It does not modify the original DeepMimic behavior.
- For detailed information about the original project, refer to the official repository.

## License

The original DeepMimic source code is licensed under the MIT License.
Copyright (c) 2018 Xue Bin Peng. See LICENSE for details.

The packaging scripts, launcher, and documentation in this distribution
are Copyright (c) 2026 OL39m2. All rights reserved.
They are not licensed under the MIT License.

## Credits

- Xue Bin Peng for the original DeepMimic project.
- All contributors to the original repository.
