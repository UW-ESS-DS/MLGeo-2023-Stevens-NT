# MLGeo-2023-Stevens-NT
NT Stevens' repository for ML in Geosciences Fall 2023 coursework codes

## Installation/Deployment
### For non Apple M1/M2 chips:
`conda env create -f environment.yml`  

### For Apple M1/M2 chipped systems
This requires a little more work to get tensorflow2 working on Apple Silicon  

Follow macOS-side installation instructions from references (1) & (2)  
    1. Install xcode command line tools  
        `xcode-select --install`  
    2. Install Miniforge - follow instructions in reference (2)  
    3. Then turn off the default base environment (ref 1)  
        `conda config --set auto_activate_base false`  
    
Run this version of `conda env create` from the comments and gist in references (3) & (4), respectively  
`CONDA_SUBDIR=osx-arm64 conda env create -f environment_AppleM1.yml`  

## Basic Usage

## Contact Information
ntsteven@uw.edu

## References
environment_AppleM1.yml install instructions based on:  
(1) https://caffeinedev.medium.com/how-to-install-tensorflow-on-m1-mac-8e9b91d93706  
(2) https://github.com/conda-forge/miniforge  
(3) https://developer.apple.com/forums/thread/711792  
(4) https://gist.github.com/corajr/a10af2f893c4b79275281f6b6fd915d3  