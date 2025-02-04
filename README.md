
thanks to [sdbds](https://github.com/sdbds) & [magic-research](https://github.com/magic-research)

TESTED SUCCESS INSTALL 2023-12-06 MINIMUM GPU 12GB (EX : RTX 3060)

SUCCESS GENERATE

https://github.com/EKI-INDRADI/magic-animate-for-windows-rnd-20231206/assets/47073420/1588088e-9cac-4693-9b76-c1463ca31055

```sh
magic-animate
|----pretrained_models (create folder pretrained_models)
  |----MagicAnimate (git lfs clone https://huggingface.co/zcxu-eric/MagicAnimate)
    |----appearance_encoder
      |----diffusion_pytorch_model.safetensors
      |----config.json
    |----densepose_controlnet
      |----diffusion_pytorch_model.safetensors
      |----config.json
    |----temporal_attention
      |----temporal_attention.ckpt
  |----sd-vae-ft-mse (manual download https://huggingface.co/stabilityai/sd-vae-ft-mse)
    |----config.json
    |----diffusion_pytorch_model.safetensors
  |----stable-diffusion-v1-5 (manual download https://huggingface.co/runwayml/stable-diffusion-v1-5, dont donload all 100GB+)
    |----scheduler
       |----scheduler_config.json
    |----text_encoder
       |----config.json
       |----pytorch_model.bin
    |----tokenizer (all)
    |----unet
       |----diffusion_pytorch_model.bin
       |----config.json
    |----v1-5-pruned-emaonly.safetensors
|----...

```

```sh
CUDA SDK 11.8 + WIN ENV (LATEST)
CUDA CUDNN 8.9 + WIN ENV  (LATEST)
FFMPEG 6 + WIN ENV (LATEST)
ANACONDA
GIT

how to setup ? go chat.openai.com
```

```sh
git clone git@github.com:EKI-INDRADI/magic-animate-for-windows-rnd-20231206.git
cd magic-animate-for-windows-rnd-20231206
conda create -n m_animate_for_win python=3.10 -y
conda activate m_animate_for_win
pip install -r requirements-windows.txt

```

how to run
```sh
# SINGLE GPU
python -m demo.gradio_animate

# MULTI GPU (ISSUE PATH)
python -m demo.gradio_animate_dist

```

how to run spesific gpu (for example , gpu:0 RTX 3080 10GB, gpu:1 RTX 3060 12GB)

```sh

# example i have 2 gpu [0,1] and run spesific GPU 1

python -m demo.gradio_animate_gpu_1

```

FOR CUSTOM :

demo\animate_gpu_1.py
```python
import os
#---- SPESIFIC CUDA
# FOR HIDDEN GPU
os.environ["CUDA_VISIBLE_DEVICES"] = str(1)
#---- SPESIFIC CUDA

#...

#...

import torch
# print('torch.cuda.device_count() CHECK CUDA DEVICES : ')
# print(torch.cuda.device_count())
# result : 1  << I HAVE 2 GPU , because CUDA_VISIBLE_DEVICES only 1


#...


#...


class MagicAnimate():
    def __init__(self, config="configs/prompts/animation.yaml") -> None:
        print("Initializing MagicAnimate Pipeline...")
        *_, func_args = inspect.getargvalues(inspect.currentframe())
        func_args = dict(func_args)
        
        config  = OmegaConf.load(config)
        
        inference_config = OmegaConf.load(config.inference_config)
            
        motion_module = config.motion_module
       
        ### >>> create animation pipeline >>> ###
        tokenizer = CLIPTokenizer.from_pretrained(config.pretrained_model_path, subfolder="tokenizer")
        text_encoder = CLIPTextModel.from_pretrained(config.pretrained_model_path, subfolder="text_encoder")
        if config.pretrained_unet_path:
            unet = UNet3DConditionModel.from_pretrained_2d(config.pretrained_unet_path, unet_additional_kwargs=OmegaConf.to_container(inference_config.unet_additional_kwargs))
        else:
            unet = UNet3DConditionModel.from_pretrained_2d(config.pretrained_model_path, subfolder="unet", unet_additional_kwargs=OmegaConf.to_container(inference_config.unet_additional_kwargs))
        self.appearance_encoder = AppearanceEncoderModel.from_pretrained(config.pretrained_appearance_encoder_path, subfolder="appearance_encoder").cuda()
        self.reference_control_writer = ReferenceAttentionControl(self.appearance_encoder, do_classifier_free_guidance=True, mode='write', fusion_blocks=config.fusion_blocks)
        self.reference_control_reader = ReferenceAttentionControl(unet, do_classifier_free_guidance=True, mode='read', fusion_blocks=config.fusion_blocks)
        if config.pretrained_vae_path :
            vae = AutoencoderKL.from_pretrained(config.pretrained_vae_path)
        else:
            vae = AutoencoderKL.from_pretrained(config.pretrained_model_path, subfolder="vae")

        #---- SPESIFIC CUDA

        # SET CONSOLE YOUR GPU
        torch.cuda.set_device(0)  

        #---- /SPESIFIC CUDA
```

after edit run

```sh

# spesific GPU 1

python -m demo.gradio_animate_gpu_1

```
[REFERENCE](https://www.bilibili.com/video/BV1ig4y1f7BQ/?share_source=copy_web&vd_source=842db193ae3e8fc29019a57821e30000)

[video to densepose magic animation](https://github.com/EKI-INDRADI/magic-animate-vid2densepose-rnd-20231206)

<!-- # magic-edit.github.io -->

<p align="center">

  <h2 align="center">MagicAnimate: Temporally Consistent Human Image Animation using Diffusion Model</h2>
  <p align="center">
    <a href="https://scholar.google.com/citations?user=-4iADzMAAAAJ&hl=en"><strong>Zhongcong Xu</strong></a>
    ·
    <a href="http://jeff95.me/"><strong>Jianfeng Zhang</strong></a>
    ·
    <a href="https://scholar.google.com.sg/citations?user=8gm-CYYAAAAJ&hl=en"><strong>Jun Hao Liew</strong></a>
    ·
    <a href="https://hanshuyan.github.io/"><strong>Hanshu Yan</strong></a>
    ·
    <a href="https://scholar.google.com/citations?user=stQQf7wAAAAJ&hl=en"><strong>Jia-Wei Liu</strong></a>
    ·
    <a href="https://zhangchenxu528.github.io/"><strong>Chenxu Zhang</strong></a>
    ·
    <a href="https://sites.google.com/site/jshfeng/home"><strong>Jiashi Feng</strong></a>
    ·
    <a href="https://sites.google.com/view/showlab"><strong>Mike Zheng Shou</strong></a>
    <br>
    <br>
        <a href="https://arxiv.org/abs/2311.16498"><img src='https://img.shields.io/badge/arXiv-MagicAnimate-red' alt='Paper PDF'></a>
        <a href='https://showlab.github.io/magicanimate'><img src='https://img.shields.io/badge/Project_Page-MagicAnimate-green' alt='Project Page'></a>
        <a href='https://huggingface.co/spaces/zcxu-eric/magicanimate'><img src='https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Spaces-blue'></a>
    <br>
    <b>National University of Singapore &nbsp; | &nbsp;  ByteDance</b>
  </p>
  
  <table align="center">
    <tr>
    <td>
      <img src="assets/teaser/t1.gif">
    </td>
    <td>
      <img src="assets/teaser/t4.gif">
    </td>
    </tr>
    <tr>
    <td>
      <img src="assets/teaser/t3.gif">
    </td>
    <td>
      <img src="assets/teaser/t2.gif">
    </td>
    </tr>
  </table>

## 📢 News
* **[2023.12.4]** Release inference code and gradio demo. We are working to improve MagicAnimate, stay tuned!
* **[2023.11.23]** Release MagicAnimate paper and project page.


## ⚒️ Installation
prerequisites: `python>=3.8`, `CUDA>=11.3`, `ffmpeg` and `git`.

Python and Git:

- Python 3.10.11: https://www.python.org/ftp/python/3.10.11/python-3.10.11-amd64.exe
- git: https://git-scm.com/download/win

- Install [ffmpeg](https://ffmpeg.org/) for your operating system
  (https://www.geeksforgeeks.org/how-to-install-ffmpeg-on-windows/)
  
  notice:step 4 use windows system Set Enviroment Path.

Give unrestricted script access to powershell so venv can work:

- Open an administrator powershell window
- Type `Set-ExecutionPolicy Unrestricted` and answer A
- Close admin powershell window

Install with Powershell run `install.ps1` or `install-cn.ps1`(for Chinese)

## 🎨 Gradio Demo 

#### Online Gradio Demo:
Try our [online gradio demo](https://huggingface.co/spaces/zcxu-eric/magicanimate) quickly.

#### Local Gradio Demo:
Launch local gradio demo on single GPU:

Powershell run with `run_gui.ps1`

Launch local gradio demo if you have multiple GPUs:

Edit `run_gui.ps1` set `$mutil_gpu=1` then run.

Then open gradio demo in local browser.

## 🙏 Acknowledgements
We would like to thank [AK(@_akhaliq)](https://twitter.com/_akhaliq?lang=en) and huggingface team for the help of setting up oneline gradio demo.

## 🎓 Citation
If you find this codebase useful for your research, please use the following entry.
```BibTeX
@inproceedings{xu2023magicanimate,
    author    = {Xu, Zhongcong and Zhang, Jianfeng and Liew, Jun Hao and Yan, Hanshu and Liu, Jia-Wei and Zhang, Chenxu and Feng, Jiashi and Shou, Mike Zheng},
    title     = {MagicAnimate: Temporally Consistent Human Image Animation using Diffusion Model},
    booktitle = {arXiv},
    year      = {2023}
}
```

