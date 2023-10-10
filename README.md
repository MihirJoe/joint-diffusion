# Removing Structured Noise with Diffusion Models
Official repository of the Removing Structured Noise with Diffusion Models paper.
The joint posterior sampling functions for diffusion models proposed in the paper can be found in [sampling.py](./generators/SGM/sampling.py) and [guidance.py](./generators/SGM/guidance.py).

If you find the code useful for your research, please cite the paper:
```bib
@article{stevens2023removing,
  title={Removing Structured Noise with Diffusion Models},
  author={Stevens, Tristan SW and Robert, Jean-Luc and Yu, Faik C and Shin, Jun Seob and van Sloun, Ruud JG},
  journal={arXiv preprint arXiv:2302.05290},
  year={2023}
}
```
## Table of contents
* [Results](#structured-denoising)
* [Inference](#inference)
* [References](#references)
# Structured denoising

#### Structured denoising on CelebA with MNIST corruption
<p align="center">
<table><tr>
<td> <img src="./images/comparison_celeba_mnist.png" alt="CelebA" style="width: 500;"/>
<h5 align="center">CelebA</h5> </td>
<td> <img src="./images/comparison_celeba_mnist_ood.png" alt="OOD" style="width: 500;"/>
<h5 align="center">Out-of-distribution dataset</h5> </td>
</tr></table>
</p>

Run the following command with  `keep_track` set to `true` in the config to generate the animation.
```bash
python inference.py -e paper/celeba_mnist_pigdm -t denoise -m glow sgm
```

<p align="center">
<table><tr>
<td> <img src="./images/celeba_mnist_denoising_animation.gif" alt="CelebA structured denoising" style="width: 400;"/>
<h5 align="center">Structured denoising animation</h5> </td>
<td> <img src="./images/celeba_mnist_sgm_flow_animation.gif" alt="FLOW vs SGM" style="width: 500;"/>
<h5 align="center">Structured denoising comparison FLOW vs Diffusion</h5> </td>
</tr></table>
</p>

#### Projection, DPS, $\Pi$GDM and Flow

<p align="center">
<table><tr>
<td> <img src="./images/comparison_celeba_mnist_proj_dps_pidgm.png" alt="CelebA" style="width: 500;"/>
<h5 align="center">CelebA</h5> </td>
<td> <img src="./images/comparison_celeba_mnist_proj_dps_pidgm_ood.png" alt="OOD" style="width: 500;"/>
<h5 align="center">Out-of-distribution dataset</h5> </td>
</tr></table>
</p>

# Inference
## install environment
Install an environment with TF2.10 and Pytorch, both GPU enabled. All the other dependencies can be found in the [requirements](./requirements) folder. See [installation environment](#installation-environment) for more detailed instructions.
## download weights
Pretrained weights should be automatically downloaded to the [./checkpoints](./checkpoints) folder, but can also be manually downloaded [here]([link-tba](https://drive.google.com/uc?id=1OxC_9MMf1W7sO2adeENpvrH2atsjThTZ)). Each dataset is a different folder in the checkpoints directory and all the different models have a separate nested folder again for each dataset. In those model folders, besides the checkpoint, a training config `.yaml` file is provided for each trained model (necessary for inference, to build the model again).

## run inference
Use the [inference.py](inference.py) script for inference.
```
usage: inference.py [-h] [-e EXPERIMENT] [-t {denoise,sample,evaluate,show_dataset}] [-n NUM_IMG] [-m [MODELS ...]] [-s SWEEP]

options:
  -h, --help            show this help message and exit
  -e EXPERIMENT, --experiment EXPERIMENT
                        experiment name located at ./configs/inference/<experiment>)
  -t {denoise,sequence_denoise,sample,evaluate,show_dataset,plot_results,run_metrics}, --task
                        which task to run
  -n NUM_IMG, --num_img NUM_IMG
                        number of images
  -m [MODELS ...], --models [MODELS ...]
                        list of models to run
  -s SWEEP, --sweep SWEEP
                        sweep config located at ./configs/sweeps/<sweep>)
  -ef EVAL_FOLDER, --eval_folder EVAL_FOLDER
                        eval file located at ./results/<eval_folder>)
```

Example:
Main experiment with CelebA data and MNIST corruption:
```bash
python inference.py -e paper/celeba_mnist_pigdm -t denoise -m bm3d nlm glow gan sgm
```
Denoising comparison with multiple models:
```bash
python inference.py -e paper/mnist_denoising -t denoise -m bm3d nlm glow gan sgm
```
Or to run a sweep
```bash
python inference.py -e paper/mnist_denoising -t denoise -m glow -s glow_sweep
```

## inference configs
All working inference configs are found in the [./configs/inference/paper](./configs/inference/paper) folder. Path to those inference configs (or just the name of them) should be provided to the `--experiment` flag when calling the [inference.py](inference.py) script.

## data
Make sure to set the `data_root` parameter in the inference config (at the top). All datasets should be (automatically) downloaded and put as a subdirectory to the specified `data_root`. More information can be found in the [datasets.py](datasets.py) docstrings.

## installation environment
Install packages in a conda environment with TF2 and Pytorch

```bash
conda create -n joint-diffusion python=3.10
conda activate joint-diffusion
python -m pip install --upgrade pip
```

To install Tensorflow ([installation guide](https://www.tensorflow.org/install/pip))
```bash
conda install -c conda-forge cudatoolkit=11.2 cudnn=8.1.0
python -m pip install "tensorflow<2.11"
# Verify install:
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
```

To install Pytorch >= 1.13 ([installation guide](https://pytorch.org/get-started/locally/))
```bash
conda install pytorch pytorch-cuda=11.7 -c pytorch -c nvidia
conda install cudatoolkit
# Verify install:
python -c "import torch; print(torch.cuda.is_available())"
```

To install the other dependencies
```bash
pip install -r requirements/requirements.txt
```
