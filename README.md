<div align="center">
    <img alt="icon" src="https://github.com/receyuki/stable-diffusion-prompt-reader/raw/master/sd_prompt_reader/resources/icon.png" width=20% height=20%>
    <h1>SD Prompt Reader Node</h1>
    <a href="https://github.com/receyuki/comfyui-prompt-reader-node/blob/master/LICENSE">
        <img alt="GitHub" src="https://img.shields.io/github/license/receyuki/comfyui-prompt-reader-node"></a>
    <img alt="GitHub tag (with filter)" src="https://img.shields.io/github/v/tag/receyuki/comfyui-prompt-reader-node?label=node">
    <a href="https://github.com/receyuki/stable-diffusion-prompt-reader">    
        <img alt="GitHub tag (with filter)" src="https://img.shields.io/github/v/tag/receyuki/stable-diffusion-prompt-reader?label=core"></a>
    <a href="https://github.com/psf/black">
        <img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>
    <br>
<blockquote>
This project is currently in beta status. If you encounter any issues or have any suggestions, please let me know.
</blockquote>
This is a subproject of the 
<a href="https://github.com/receyuki/stable-diffusion-prompt-reader">SD Prompt Reader.</a>
It helps you extract metadata from images in any format supported by the 
<a href="https://github.com/receyuki/stable-diffusion-prompt-reader">SD Prompt Reader</a> and saves the images with 
additional metadata to ensure compatibility with metadata detection on websites such as Civitai.
    <br>
  <p>
    <a href="#supported-formats">Supported Formats</a> •
    <a href="#installation">Installation</a> •
    <a href="#usage">Usage</a> •
    <a href="#credits">Credits</a>
  </p>
    <img src="./images/screenshot_v100.png">
</div>


## Supported Formats
|                                                                          | PNG | JPEG | WEBP | TXT* |
|--------------------------------------------------------------------------|:---:|:----:|:----:|:----:|
| [A1111's webUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) |  ✅  |  ✅   |  ✅   |  ✅   |
| [Easy Diffusion](https://github.com/easydiffusion/easydiffusion)         |  ✅  |  ✅   |  ✅   |      |
| [StableSwarmUI](https://github.com/Stability-AI/StableSwarmUI)*          |  ✅  |  ✅   |      |      |
| [Fooocus-MRE](https://github.com/MoonRide303/Fooocus-MRE)*               |  ✅  |  ✅   |      |      |
| [InvokeAI](https://github.com/invoke-ai/InvokeAI)                        |  ✅  |      |      |      |
| [ComfyUI](https://github.com/comfyanonymous/ComfyUI)*                    |  ✅  |      |      |      |
| [NovelAI](https://novelai.net/)                                          |  ✅  |      |      |      |
| [Draw Things](https://drawthings.ai/)                                    |  ✅  |      |      |      |
| Naifu(4chan)                                                             |  ✅  |      |      |      |

See [SD Prompt Reader](https://github.com/receyuki/stable-diffusion-prompt-reader#supported-formats) for details

## Installation
>While ZIP package is available, it is strongly recommended not to use it for installation.
### Install and update via [ComfyUI Manager](https://github.com/ltdrdata/ComfyUI-Manager) (Recommended)
Search for `SD Prompt Reader` in the ComfyUI Manager and install it.
### Install manually
Please make sure to install the submodules along with the main repository.
1. `cd` to the `custom_node` folder
2. Clone this repo
    ```shell
    git clone --recursive https://github.com/receyuki/comfyui-prompt-reader-node.git
    ```
3. Install dependencies
    ```shell
    cd comfyui-prompt-reader-node
    pip install -r requirements.txt
    ```
#### Update
When updating, don't forget to include the submodules along with the main repository.
```shell
git pull --recurse-submodules
```

## Usage
### Prompt Reader Node
- The `Prompt Reader` Node works exactly the same as the 
[standalone SD Prompt Reader](https://github.com/receyuki/stable-diffusion-prompt-reader). 
It uses the Image Data Reader from the 
[standalone SD Prompt Reader](https://github.com/receyuki/stable-diffusion-prompt-reader), 
allowing it to support the same formats and receive updates along with the 
[SD Prompt Reader](https://github.com/receyuki/stable-diffusion-prompt-reader).
- Due to custom nodes and complex workflows potentially causing issues with SD Prompt Reader's ability 
to read image metadata correctly, it is recommended to embed the `Prompt Saver` Node within the workflow 
to ensure maximum compatibility.   
- For images containing multiple sets of parameters, such as those processed through `hires-fix` or `refiner`, 
you will need to modify the `parameter_index` to select the parameters you need
- For images generated by SDXL and containing multiple sets of prompts, 
the `text_g` will be combined with `text_l` into a single prompt

<div align="center">
   <img src="./images/reader.png" width="25%" height="25%" alt="reader node">
</div>

### Prompt Saver Node
- The `Prompt Saver` Node and The `Parameter Generator` Node are designed to be used together.  
- The `Prompt Saver` Node will write additional metadata in the A1111 format to the output images 
to be compatible with any tools that support the A1111 format, 
including SD Prompt Reader and Civitai. 
Due to custom nodes and complex workflows potentially causing issues with SD Prompt Reader's ability 
to read image metadata correctly, it is recommended to embed this node within the workflow 
to ensure maximum compatibility.   
- Since it's not possible to directly extract metadata from `KSampler`, it is necessary to 
use the `Parameter Generator` Node to generate parameters and simultaneously output them to both 
the `Prompt Saver` Node and `KSampler`.
- Please refer to the following table for placeholders supported by the `filename` and `path`.

   | Placeholder |
   |-------------|
   | %date       |
   | %time       |
   | %counter    |
   | %seed       |
   | %steps      |
   | %cfg        |
   | %extension  |
   | %model      |
   | %sampler    |
   | %scheduler  |
   | %quality    |

- For the `date_format` and `time_format`, please refer to 
[strftime.org](https://strftime.org/) or [www.strfti.me](https://www.strfti.me/).

<div align="center">
   <img src="./images/saver.png" width="25%" height="25%" alt="generator and saver node">
</div>

## Parameter Generator Node
- Since it's not possible to directly extract metadata from `KSampler`, it is necessary to 
use the `Parameter Generator` Node to generate parameters and simultaneously output them to both 
the `Prompt Saver` Node and `KSampler`.
- The `model_version` and `aspect_ratio` are used only for calculating the optimal resolution of the selected model version 
under the chosen aspect ratio. The calculation method is based on the 
[Stability AI development documentation](https://platform.stability.ai/docs/features/api-parameters#about-dimensions) 
and the [StableSwarmUI source code](https://github.com/Stability-AI/StableSwarmUI) (developed by Stability AI).
- `refiner_start` refers to the proportion of steps completed when the refiner starts running, 
i.e., the proportion of base steps to total steps. This is used to calculate the `start_at_step` (`REFINER_START_STEP`)
required by the refiner `KSampler` under the selected step ratio.

<div align="center">
   <img src="./images/generator.png" width="25%" height="25%" alt="generator and saver node">
</div>

### Prompt Merger Node & Type Converter Node
- Since the A1111 format cannot store `text_g` and `text_l` separately, SDXL users need to use 
the `Prompt Merger` Node to combine `text_g` and `text_l` into a single prompt.
- Since `model_name`, `sampler_name`, and `scheduler` are special types 
that cannot be directly used by some other nodes,
You can use the `Type Converter` Node to convert them into `STRING` type.

<div align="center">
   <img src="./images/merger_converter.png" width="25%" height="25%" alt="merger and converter node">
</div>

### [Example Workflow](./workflows/example_workflow.json)
>The example workflow is outdated and will be updated in the stable release.

<div align="center">
   <img src="./images/example_workflow.png" width="100%" height="100%" alt="example workflow">
</div>

## Credits
- The SD Prompt Reader node is based on [ComfyUI Load Image With Metadata](https://github.com/tkoenig89/ComfyUI_Load_Image_With_Metadata)
- The SD Prompt Saver node is based on [Comfy Image Saver](https://github.com/giriss/comfy-image-saver) & [Stable Diffusion Webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
- The seed generator in the SD Parameter Generator is modified from [rgthree's Comfy Nodes](https://github.com/rgthree/rgthree-comfy#rgthrees-comfy-nodes)
- A special thanks to [@alessandroperilli](https://github.com/alessandroperilli) and his [AP Workflow](https://perilli.com/ai/comfyui/) for providing numerous suggestions