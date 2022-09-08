# Convolution Benchmarks
![image](https://user-images.githubusercontent.com/74956/188198140-b109f11f-f9e6-4664-81da-2b2bc754435b.png)
# Pre-requisites
Create a virtual environment and install python dependencies.
```
python3 -m venv ~/venv/convperf
source ~/venv/convperf/bin/activate
pip install -r requirements.txt
```
For multi-threaded libxsmm, you will need to install OpenMP. You will have to install
the appropriate package for your compiler. For example, if your compiler is clang++-14,
then you can install OpenMP by doing the following.
```
sudo apt install libomp-14-dev
```
# Build Instructions
```
cmake -GNinja -B build .
cmake --build build
```
# Run Benchmarks
```
python convperf.py --benchmark_tool build/tools/benchmark_conv --runners iree,xsmm
```

# Extracting generated artifacts
The mlir file that contains the convolutions can be found in
the build directory in build/iree/convs.mlir. This file contains
functions for all the sizes specified in the benchmark sizes file.
Sample mlir is shown below.

```
func.func @conv2d_1x230x230x3_7x7x3x64(%arg0: tensor<1x230x230x3xf32>, %arg1: tensor<7x7x3x64xf32>) -> tensor<1x112x112x64xf32> {
  %cst_0 = arith.constant 0.000000e+00 : f32
  %0 = linalg.init_tensor [1, 112, 112, 64] : tensor<1x112x112x64xf32>
  %1 = linalg.fill ins(%cst_0 : f32) outs(%0 : tensor<1x112x112x64xf32>) -> tensor<1x112x112x64xf32>
  %2 = linalg.conv_2d_nhwc_hwcf {dilations = dense<1> : tensor<2xi64>, strides = dense<2> : tensor<2xi64>} ins(%arg0, %arg1 : tensor<1x230x230x3xf32>, tensor<7x7x3x64xf32>) outs(%1 : tensor<1x112x112x64xf32>) -> tensor<1x112x112x64xf32>
  return %2 : tensor<1x112x112x64xf32>
}
```
