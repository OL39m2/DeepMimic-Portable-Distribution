# DeepMimic Portable Distribution

A self-contained, portable Windows x64 distribution of DeepMimic, designed to eliminate the complex setup process required to run the original project. It includes an embedded Python environment, all necessary dependencies, and simple launchers for both running pre-trained policies and training new ones.

## Original Project

This distribution is based on **DeepMimic** by Xue Bin Peng. The original source code is available at [https://github.com/xbpeng/DeepMimic](https://github.com/xbpeng/DeepMimic). The simulation, scenes, and learning algorithm are unmodified from the original repository. A small number of Python files were modified to support checkpoint resume and Windows compatibility; see "Modifications to Original Code" for details.

## Contents

- Embedded Python 3.7 (64-bit)
- Required Python packages: TensorFlow 1.13.1, NumPy, PyOpenGL, MPI4Py, etc.
- Prebuilt `_DeepMimicCore.pyd` (compiled from the original source)
- Original `args` and `data` directories
- All necessary runtime DLLs (freeglut, GLEW, etc.)
- Launcher (`run.bat`)
- Launcher for training new policies (`train.bat`)
- Microsoft MPI installers included in the `prerequisites/` folder
- Motion converter (`convert_motion.bat` + `motion_converter/`)
- `InputBvh/` folder for user-provided BVH files
- `data/motions/custom/` folder for converted motions
- Sample args for custom motions (`train_custom_motion_args.txt`, `run_custom_args.txt`)

## Build Notes

- The _DeepMimicCore.pyd included in this distribution was compiled from the original DeepMimic source code, without modification. The build was performed in a new Visual Studio project, not the DeepMimicCore.sln solution provided with the original repository. This was done to allow full control over include paths, library paths, and linker settings.
- No changes were made to the original C++ source code or simulation logic. Three Python files (`learning/tf_agent.py`, `learning/rl_agent.py`, and `mpi_run.py`) were modified to support checkpoint resume and Windows compatibility; see the "Modifications to Original Code" section.

### Dependencies

The following dependencies are used by this distribution. Bullet and freeglut were built from source using CMake. The remaining libraries were either header-only, pre-built, installed via pip, or installed separately.

| Library | Version | Source / Method | Notes |
| :--- | :--- | :--- | :--- |
| Bullet | 2.88 | Built from source (CMake) | `USE_DOUBLE_PRECISION` disabled |
| Eigen | 3.3.7 | Header-only | No build required |
| freeglut | 3.0.0 | Built from source (CMake) | |
| GLEW | 2.1.0 | Pre-built binaries | x64 |
| Python | 3.7.0 | Embedded distribution | 64-bit |
| SWIG | 4.0.0 | Pre-built executable | Used to generate the Python wrapper |
| TensorFlow | 1.13.1 | Installed via pip | |
| Microsoft MPI | v10.1.2 | Installed via Microsoft installer | Required for both running and training |

### Build Steps

1. The original C++ source files were copied into a new Visual Studio project.
2. Include directories were configured for Bullet, Eigen, freeglut, GLEW, and Python.
3. Library directories were configured to point to the `.lib` files produced by the CMake builds.
4. Additional dependencies were specified in the linker settings: `BulletCollision.lib`, `BulletDynamics.lib`, `LinearMath.lib`, `freeglut.lib`, `glew32.lib`, and `python37.lib`.
5. The preprocessor definition `_CRT_SECURE_NO_WARNINGS` and `_USE_MATH_DEFINES` were added.
6. The runtime library was set to `Multi-threaded (/MT)` to match the Bullet build.
7. SWIG was invoked manually to generate `DeepMimicCore_wrap.cxx` and `DeepMimicCore.py`:
```
swig -c++ -python -o DeepMimicCore_wrap.cxx DeepMimicCore.i
```
8. The generated wrapper was added to the project, with precompiled headers disabled for that file.
9. The project was built in `Release` mode for `x64`, producing `_DeepMimicCore.pyd`.
10. The resulting `.pyd` was placed alongside `DeepMimicCore.py` in the distribution root.

### Notes
- The build was performed with Visual Studio 2026 (version 18.9.2).
- The `protobuf` package was pinned to version `3.20.1` to remain compatible with TensorFlow 1.13.1.
- No changes were made to the original C++ source code or simulation logic. The learning algorithm itself is unmodified; only the checkpoint save/load mechanism was extended (see "Modifications to Original Code").

## Usage

1. Extract the archive to a directory of your choice.
2. Run `run.bat`. 
3. Select a scene from the menu.
4. The simulation window will open. Use the mouse to control the camera and interact with the character.

### Training New Policies

Training new policies requires Microsoft MPI. The installers are included in the `prerequisites/` folder.

1. Navigate to the `prerequisites/` folder.
2. Run `msmpisetup.exe` (Runtime).
3. Run `msmpisdk.msi` (SDK).
4. Restart your computer.
5. Run `train.bat`.
6. Select a training task and specify the number of workers.

Training progress is displayed in the console window. No visualization window is opened during training, as rendering is disabled to maximize performance. Training output, including checkpoint files, is saved to the `output/` directory.

## Adding Custom Motions

The distribution includes a converter that transforms BVH motion capture files into the DeepMimic JSON format.

### Steps

1. Place your `.bvh` file in the `InputBvh/` folder.
2. Run `convert_motion.bat` and select the file from the menu.
3. The converted file is saved to `data/motions/custom/` with the same base name.
4. Run `run.bat` or `train.bat` and select the "Custom motion" option.
5. For training, select the motion from the list, choose the number of workers, and choose whether to start a new training session or resume from a checkpoint.

The launcher passes `--motion_file` on the command line, which overrides the value in the args file. If you encounter issues, you can also edit the `--motion_file` line in `args/train_custom_motion_args.txt` or `args/run_custom_args.txt` directly.

### Compatibility

The converter is configured for **SFU MoCap Database** BVH files. Files from other sources (e.g., CMU) use different joint names and will fail with `LookupError: Joint name not found`. In that case, `motion_converter/settings.json` must be edited manually to map the joint names.

This feature is **experimental**. Results depend on the quality of the BVH file and the joint mapping. Training typically requires several million samples and may take several hours on a small number of workers.

### Data Source

The sample motion (`0005_Jogging001.txt`) was converted from a BVH file obtained from the **SFU Motion Capture Database** (https://mocap.cs.sfu.ca/). The original data is subject to SFU's terms of use. Users who wish to redistribute converted motions should verify the licensing terms with SFU.

### Included Sample

The distribution includes a sample custom motion (`0005_Jogging001.txt`) and its trained checkpoint (`output/agent0_model.ckpt`) as a demonstration of the custom motion pipeline.

- **Source**: SFU MoCap Database BVH file
- **Training**: ~19.5 million samples, 2-3 workers, approximately 19–22 hours
- **Training metrics**: `Train_Return` ≈ 43–45, `State_Std` ≈ 1.02, `Test_Return` ≈ 123

Note: The high `Train_Return` reflects the cumulative task reward over a long episode (the character walks continuously). It does not directly indicate motion fidelity to the reference.

The sample serves as a reference for the expected training duration and result quality on a small number of workers. Results will vary depending on the BVH file and the joint mapping.

## Notes

- This distribution is intended for evaluation, testing, and research use of DeepMimic.
- The launcher simply invokes the embedded Python interpreter with the appropriate arguments. It does not modify the original DeepMimic behavior.
- For detailed information about the original project, refer to the official repository.
- Rendering during training is not supported. To inspect training progress, run `DeepMimic.py` with `--model_files output/agent0_model.ckpt` from the command line (or edit `args/run_custom_args.txt` and use `run.bat` → Custom motion).

## Modifications to Original Code

The simulation, scenes, and the learning algorithm itself are unmodified from the original DeepMimic repository. The modifications listed below do not change how the agent learns or how the simulation behaves; they only add supporting functionality.

### Modified files

- `learning/tf_agent.py`: `save_model` now writes an accompanying `_state.json` file next to each checkpoint. `load_model` reads this file and restores the iteration counter and total sample count.
- `learning/rl_agent.py`: `_train` adds the restored sample count to the running total, so the exploration and learning-rate annealing schedule continues from where it stopped.
- `mpi_run.py`: `python3` replaced with the path to the embedded Python interpreter for Windows compatibility.

### Added files (not modifications to the original code)

- `run.bat`, `train.bat` (launchers)
- `convert_motion.bat`, `motion_converter/` (BVH converter)
- `args/train_custom_motion_args.txt`, `args/run_custom_args.txt` (templates)
- `prerequisites/` (Microsoft MPI installers)
- `InputBvh/`, `data/motions/custom/` (folders for user motions)

## License

The original DeepMimic source code is licensed under the MIT License.
Copyright (c) 2018 Xue Bin Peng. See LICENSE for details.

The packaging scripts, launcher, and documentation in this distribution
are Copyright (c) 2026 OL39m2 and are licensed under the MIT License.

The Microsoft MPI installers included in the `prerequisites/` folder are redistributed under the terms of the Microsoft MPI License Agreement. For full terms and conditions, refer to the license agreement included with the Microsoft MPI installer.


## Credits

- Xue Bin Peng for the original DeepMimic project.
- All contributors to the original repository.
