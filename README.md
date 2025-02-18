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
