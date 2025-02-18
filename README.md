# PicoGK Headless Runtime in Colab

## Overview

This repository contains a Colab notebook that demonstrates how to run PicoGK, a powerful geometry kernel, in headless mode within the Google Colaboratory environment. This allows you to perform geometric operations and computations without a graphical user interface, making it ideal for automated workflows, cloud-based processing, and batch processing.  This example creates a simple beam and saves it as an STL file.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)]([YOUR_NOTEBOOK_LINK_HERE])  <!-- Replace with your notebook's GitHub URL -->

## What is PicoGK?

PicoGK is a robust geometry kernel designed for various applications. It provides a comprehensive set of tools for creating, manipulating, and analyzing geometric models. This notebook focuses on using PicoGK programmatically, without needing a traditional desktop application.

## Purpose of this Notebook

This notebook provides a step-by-step guide to setting up and running PicoGK in a Colab environment. It handles:

*   Cloning the necessary PicoGK Runtime repository.
*   Installing required dependencies for headless operation.
*   Building a basic .NET console application to interact with PicoGK.
*   Creating a simple geometric object (a beam) using PicoGK within the .NET application.
*   Saving the resulting geometry as an STL file.

## How to Use

1.  **Open in Colab:** Click the "Open in Colab" badge above. This will open the `[your_notebook_name].ipynb` file in Google Colaboratory.  **Remember to replace `[YOUR_NOTEBOOK_LINK_HERE]` with the actual URL of your notebook on GitHub.**
2.  **Run the Notebook:** Execute the code cells sequentially from top to bottom. Each cell performs a specific task in setting up and running PicoGK and creating the example geometry. The output of each cell provides feedback on the installation and execution process.
3.  **Dependencies:**
    *   This notebook requires the PicoGK Runtime library and its dependencies. The notebook automatically downloads and installs these dependencies.
    *   The notebook also relies on .NET SDK. The required version is included in the default Colab image.
4.  **Output:**
    *   The notebook generates an STL file named `picogk.stl` in the `/content/PicoGK_Colab` directory. You can download this file from Colab after running the notebook.
5.  **Important Notes:**
    *   The installation process might take a few minutes to complete.
    *   The notebook assumes a Linux environment (provided by Colab).
    *   Some steps require `sudo` privileges, which are available in Colab.
    *   The notebook is structured to provide informative output at each stage. If a step fails, carefully examine the output for error messages.

## Code Walkthrough

The notebook is divided into two main code sections:

### 1. PicoGK Runtime Setup

```python
!git clone --recursive https://github.com/michaelp91-dev/PicoGKRuntime.git
!sudo chmod +x PicoGKRuntime/Install_Dependencies/linux_x64.sh
!./PicoGKRuntime/Install_Dependencies/linux_x64.sh

!sudo cp PicoGKRuntime/build/lib/*.so /usr/local/lib/
!sudo chmod +x /usr/local/lib/picogk.so
!ldconfig

!dotnet new console -n PicoGK_Colab
%cd PicoGK_Colab
!dotnet build
!git clone https://github.com/michaelp91-dev/PicoGK.git
```
*   **`!git clone --recursive https://github.com/michaelp91-dev/PicoGKRuntime.git`**: Clones the PicoGKRuntime repository, containing binaries and scripts for headless operation. The `--recursive` flag includes submodules.

*   **`!sudo chmod +x PicoGKRuntime/Install_Dependencies/linux_x64.sh`**: Makes the installation script executable.

*   **`!./PicoGKRuntime/Install_Dependencies/linux_x64.sh`**: Executes the installation script, installing required dependencies for the native PicoGK library.

*   **`!sudo cp PicoGKRuntime/build/lib/*.so /usr/local/lib/`**: Copies the PicoGK shared object library (`.so` file) to the system's library directory.

*   **`!sudo chmod +x /usr/local/lib/picogk.so`**: Makes the PicoGK library executable (not strictly necessary but doesn't hurt).

*   **`!ldconfig`**: Updates the system's dynamic linker cache, allowing the system to find the PicoGK library.

*   **`!dotnet new console -n PicoGK_Colab`**: Creates a new .NET console application named "PicoGK_Colab".

*   **`%cd PicoGK_Colab`**: Changes the current directory to the .NET project directory (Colab-specific magic command).

*   **`!dotnet build`**: Builds the .NET console application.

*   **`!git clone https://github.com/michaelp91-dev/PicoGK.git`**: Clones the main PicoGK repository (may contain examples or documentation).

### 2. PicoGK Geometry Generation and STL Export

```python
import os
import subprocess

# Specify the desired directory path
project_directory = "/content/PicoGK_Colab"
os.chdir(project_directory)
current_directory = os.getcwd()
print(f"Current working directory: ")

def CreateProgramCS(file_name, code):
  with open(file_name, 'w') as f:
    f.write(code)

  print("Program.cs has been written")

ProgramCode = """
using PicoGK;
using System.Numerics;
using System;
try
{
    using (Library oLibrary = new(0.1f))
    {
      Lattice lat = new();
      lat.AddBeam(  new(0,0,0),
                    new(50,0,0),
                    10,
                    10,
                    true);
      Voxels vox = new(lat);
      vox.mshAsMesh().SaveToStlFile("picogk.stl");
    }
}

catch (Exception e)
{
    Console.Write(e.ToString());
}
"""

CreateProgramCS('Program.cs', ProgramCode)

# Execute 'dotnet run" to run the application
run_process = subprocess.run(['dotnet', 'run'], capture_output=True, text=True, cwd=project_directory)
if run_process.returncode == 0:
  print("dotnet run executed successfully!")
else:
  print(f"Error executing dotnet run:\n{run_process.stderr}")
```

*   **`import os, import subprocess`**: Imports necessary Python modules for file system operations and running subprocesses.

*   **`project_directory = "/content/PicoGK_Colab" ... os.chdir(project_directory)`**: Sets the working directory to the .NET project directory. This ensures that the subsequent commands are executed in the correct location.

*   **`def CreateProgramCS(file_name, code): ...`**: Defines a helper function to create and write content to a file. In this case, it's used for the `Program.cs` file.

*   **`ProgramCode = """..."""`**: Defines a multi-line string containing the C# code that interacts with PicoGK. This code:
    *   Creates a PicoGK `Library` instance.
    *   Creates a `Lattice` (a voxel-based representation).
    *   Adds a beam to the lattice.
    *   Converts the lattice to voxels and then to a mesh.
    *   Saves the mesh as an STL file named `picogk.stl`.

*   **`CreateProgramCS('Program.cs', ProgramCode)`**: Creates or overwrites the `Program.cs` file with the specified C# code.

*   **`run_process = subprocess.run(['dotnet', 'run'], ...)`**: Executes the `dotnet run` command to run the .NET application. The `capture_output=True` argument captures the output (both standard output and standard error) of the command. The `text=True` argument decodes the output as text. The `cwd=project_directory` argument specifies the working directory for the command.  **This is the crucial command that builds and runs the .NET code.**

*   **`if run_process.returncode == 0: ...`**: Checks the return code of the `dotnet run` command. If the return code is 0, the command executed successfully. Otherwise, an error occurred, and the error message is printed.

### 3. Debugging and Error Messages

Sometimes, the .NET code in the second block might fail to run without providing clear error messages within the Colab notebook output. In such cases, executing the following command in a separate cell can help reveal the underlying errors:

```python
!dotnet run
```

*   **`!dotnet run`**:  This command attempts to run the compiled .NET application again.  By running it in a separate cell, the output is often more verbose and may reveal more detailed error messages than the initial execution in the second code block. If the second block runs without apparent errors, but no `picogk.stl` file is generated, this debugging step can be crucial for identifying the cause of the problem. The error messages may indicate issues with dependencies, file paths, or the PicoGK library itself. Make sure that after running the second block of code, you run this block and then read it. It may be a long log, but this often can provide context as to why the beam was not created.


