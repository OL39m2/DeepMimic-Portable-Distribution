# DeepMimic Portable Distribution

A self-contained, portable Windows x64 distribution of DeepMimic, designed to eliminate the complex setup process required to run the original project. It includes an embedded Python environment, all necessary dependencies, and simple launchers for both running pre-trained policies and training new ones.

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
- Launcher for training new policies `train.bat`.
- Microsoft MPI installers included in the `prerequisites/` folder.


### Training New Policies

Training new policies requires Microsoft MPI. The installers are included in the `prerequisites/` folder.

1. Navigate to the `prerequisites/` folder.
2. Run `msmpisetup.exe` (Runtime).
3. Run `msmpisdk.msi` (SDK).
4. Restart your computer.
5. Run `train.bat`.
6. Select a training task and specify the number of workers.

Training progress is displayed in the console window. No visualization window is opened during training, as rendering is disabled to maximize performance. Training output, including checkpoint files, is saved to the `output/` directory.

### Training on Custom Motion Capture Data

Training on custom motion capture data requires the data to be converted into the DeepMimic JSON format before it can be used.

1. Prepare your motion capture data in either **BVH** or **FBX** format.
2. Convert the data to DeepMimic JSON format using an appropriate conversion tool. The following tools are available:
   - **BvhToDeepMimic**: Converts BVH files to DeepMimic JSON format.
   - **FbxToMimic**: Converts FBX files to DeepMimic JSON format (under development).
3. Place the converted JSON file in the `data/motions/` directory.
4. Create a new training args file (or copy an existing one) and update the `--motion_file` parameter to point to your new motion file.
5. Update the `--char_file` and `--scene` parameters if your motion data uses a different character or scene.
6. Run the training script with your new args file.

Note that the conversion process requires manual rigging, where each bone in your motion capture skeleton must be mapped to the corresponding joint in the DeepMimic character model. Incorrect rigging will result in distorted motion.


## Notes

- This distribution is intended for evaluation, testing, and research use of DeepMimic.
- The launcher simply invokes the embedded Python interpreter with the appropriate arguments. It does not modify the original DeepMimic behavior.
- For detailed information about the original project, refer to the official repository.


## License

The original DeepMimic source code is licensed under the MIT License.
Copyright (c) 2018 Xue Bin Peng. See LICENSE for details.

The packaging scripts, launcher, and documentation in this distribution
are Copyright (c) 2026 OL39m2. All rights reserved.
They are not licensed under the MIT License.

The Microsoft MPI installers included in the `prerequisites/` folder are redistributed under the terms of the Microsoft MPI License Agreement. For full terms and conditions, refer to the license agreement included with the Microsoft MPI installer.


## Credits

- Xue Bin Peng for the original DeepMimic project.
- All contributors to the original repository.