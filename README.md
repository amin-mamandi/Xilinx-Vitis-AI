# XILINX_VITIS_AI

**This repo will help you to Deploy your Deep Learning Model on Ultra96v2 Board.**
*************
First we should know what are the requirements?



1. Vitis Core Development Kit 2019.2
> This could be downloaded from here: [Link to the websire](https://downloadly.ir/software/engineering-specialized/xilinx-vivado-design-suite/)
2. Vitis-AI GitHub Repository v1.1
> Here is the link to the repository [v1.1](https://github.com/Xilinx/Vitis-AI/tree/v1.1)
3. Vitis-Ai Docker Container
> The command to pull the container: docker pull xilinx/vitis-ai:1.1.56
4. XRT 2019.2
> GitHub Repo Link [2019.2](https://github.com/Xilinx/XRT/tree/2019.2)
5. Avnet Vitis Platform 2019.2
> Here is the link to download the zip file [Avnet Website](http://avnet.me/ultra96v2-vitis-2019.2)

*******

Once the tools have been setup, there are five (5) main steps to targeting an AI applications to Ultra96V2 Platform:

1. Build the Hardware Design
2. Compile Your Custom Model 
3. Build the AI Applications
4. Create the SD Card Content
5. Execute the AI Applications on hardware

*****************

Supposed that you have trained your model previously in one of the Tensorflow (.Pb), Caffe(.Caffemodel and .Prototxt) and Darknet(.Weights and .Cfg) Frameworks.

**Build the Hardware Design**

Clone Xilinx’s Vitis-AI github repository:

```
$ git clone --branch v1.1 https://github.com/Xilinx/Vitis-AI
$ cd Vitis-AI
$ export VITIS_AI_HOME = "$PWD"
```

Install the Avnet Vitis platform:> 

>  Download [this](http://avnet.me/ultra96v2-vitis-2019.2) and extract to the hard drive of your linux machine. Then, specify the location of the Vitis platform, by creating the SDX_PLATFORM environment variable that specified to the location of the.xpfm file.


```
$ export SDX_PLATFORM=/home/Avnet/vitis/platform_repo/ULTRA96V2/ULTRA96V2.xpfm
```

**Build the Hardware Project (SD Card Image)**

> I suggest you to download the Pre-Built from [here](http://avnet.me/ultra96v2-vitis-ai-1.1-image)

**Compile the Trained Models**

> Remember that you should have pulled the docker container first. 

Caffe Models:
```
$ cd $VITIS_AI_HOME
$ mkdir project
$ cp PATH/TO/TRAINED/MODELS  $VITIS_AI_HOME/project
$ ./docker_run.sh xilinx/vitis-ai:1.1.56
$ cd project
$ conda activate vitis-ai-caffe
$ vai_q_caffe quantize -model float.prototxt -weights float.caffemodel -calib_iter 5
$ vai_c_caffe -p .PROTOTXT -c .CAFFEMODEL -a ARCH.JSON -o OUTPUT_DIR -n NET_NAME 
```
Tensorflow Models:
```
$ cd $VITIS_AI_HOME
$ mkdir project
$ cp PATH/TO/TRAINED/MODELS  $VITIS_AI_HOME/project
$ ./docker_run.sh xilinx/vitis-ai:1.1.56
$ cd project
$ conda activate vitis-ai-tensorflow
$ vai_q_tensorflow quantize --input_frozen_graph FROZEN_PB --input_nodes xxx --output_nodes yyy --input_shapes zzz --input_fn module.calib_input --calib_iter 5
$ vai_c_tensorflow -f FROZEN_PB -a ARCH.JSON -o OUTPUT_DIR -n NET_NAME 

```
**Compile the AI Application Using DNNDK APIs**
> The DNNDK API is the low-level API used to communicate with the AI engine (DPU). This API is the recommended API for users that will be creating their own custom neural networks.

Download and install the SDK for cross-compilation, specifying a unique and meaningful installation destination (knowing that this SDK will be specific to the Vitis-AI 1.1 DNNDK samples):
```
$ wget -O sdk.sh https://www.xilinx.com/bin/public/openDownload?filename=sdk.sh
$ chmod +x sdk.sh
$ ./sdk.sh -d ~/petalinux_sdk_vai_1_1_dnndk 
```

Setup the environment for cross-compilation:
```
$ unset LD_LIBRARY_PATH
$ source ~/petalinux_sdk_vai_1_1_dnndk/environment-setup-aarch64-xilinx-linux
```

Download and extract the DNNDK runtime examples and Install the additional DNNDK runtime content:
```
$ wget -O vitis-ai_v1.1_dnndk.tar.gz  https://www.xilinx.com/bin/public/openDownload?filename=vitis-ai_v1.1_dnndk.tar.gz
$ tar -xvzf vitis-ai-v1.1_dnndk.tar.gz
$ cd vitis-ai-v1.1_dnndk
$ ./install.sh $SDKTARGETSYSROOT
```
Copy the Compiled project:
```
$ cp -r ../project/ .
```

Download and extract the additional content (images and video files) for the DNNDK examples:
```
$ wget -O vitis-ai_v1.1_dnndk_sample_img.tar.gz https://www.xilinx.com/bin/public/openDownload?filename=vitis-ai_v1.1_dnndk_sample_img.tar.gz
$ tar -xvzf vitis-ai_v1.1_dnndk_sample_img.tar.gz
```
For the custom application (project folder), create a model directory and copy the dpu_\*.elf model files you previously built:
```
$ cd $VITIS_AI_HOME/project
$ mkdir model_for_ultra96v2
$ cp -r model_for_ultra96v2 model
$ make
```

NOTE: You could also edit the build.sh script to add support for the new Platforms like Ultra96V2.

**Execute the AI Application on ULTRA96V2**

1.  Boot the Ultra96V2 with the pre-build sd-card image you dowloaded. [For Learning How to Do This, Click HERE!](https://github.com/aminmamandi/XILINX_VITIS_AI/blob/main/SD-CARD-FLASHING.md)
2.  ```  $ cd /run/media/mmcblk0p1 ```
3.  ```  $ cp dpu.xclbin /usr/lib/.```
4.  Install the Vitis-AI embedded package:
 ``` 
$ cd runtime/vitis-ai_v1.1_dnndk 
$ source ./install.sh
```
          
5. Define the DISPLAY environment variable:
```
$ export DISPLAY=:0.0
$ xrandr --output DP-1 --mode 640x480
```
6. Run the Custom Application:
```
 $ cd vitis_ai_dnndk_samples
 $ ./App 
 ```
