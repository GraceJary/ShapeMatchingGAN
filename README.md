# ShapeMatchingGAN

<img src="https://github.com/williamyang1991/TET-GAN/blob/master/imgs/teaser.png" width="80%" height="80%">

This is a pytorch implementation of the paper.

Shuai Yang, Zhangyang Wang, Zhaowen Wang, Ning Xu, Jiaying Liu and Zongming Guo.

Controllable Artistic Text Style Transfer via Shape-Matching GAN, 

Accepted by International Conference on Computer Vision (ICCV), 2019.

[[Project]](https://williamyang1991.github.io/projects/ICCV2019/SMGAN.html) | [[Paper]](https://arxiv.org/abs/1905.01354) 

It is provided for educational/research purpose only. Please consider citing our paper if you find the software useful for your work.


## Usage: 

#### Prerequisites
- Python 2.7
- Pytorch 1.1.0
- matplotlib
- scipy
- Pillow

#### Install
- Clone this repo:
```
git clone https://github.com/williamyang1991/ShapeMatchingGAN.git
cd ShapeMatchingGAN/src
```
## Testing Example

- Download pre-trained models from [[Google Drive]](https://drive.google.com/open?id=1gjHR39deUSPChtRbKAD80waoQFTiXyMs) or [[Baidu Cloud]](https://pan.baidu.com/s/11LVKWAd6BCgWQqM6SZByEQ) to `../save/`
- Artisic text style transfer using <i>fire</i> style with scale 0.0
  - Results can be found in `../output/`

<img src="https://github.com/williamyang1991/TET-GAN/blob/master/imgs/example.jpg" width="50%" height="50%">

```
python test.py \
--scale 0.0
--structure_model ../save/fire-GS-iccv.ckpt \
--texture_model ../save/fire-GT-iccv.ckpt \
--gpu
```
- Artisic text style transfer with specified parameters
  - setting scale to -1 means testing with multiple scales in \[0,1\] with step of scale_step
  - specify the input text name, output image path and name with text_name, result_dir and name, respectively
```
python test.py \
--text_name ../data/rawtext/yaheiB/val/0801.png \
--scale -1 --scale_step 0.2 \
--structure_model ../save/fire-GS-iccv.ckpt \
--texture_model ../save/fire-GT-iccv.ckpt \
--result_dir ../output --name fire-0801 \
--gpu
```
or just modifying and running
```
sh ../script/launch_test.sh
```
- For black and white text images, use option `--text_type 1`
  - utils.text_image_preprocessing will transform BW images into distance-based images
  - distance-based images make the network better deal with the saturated regions

## Training Examples

### Training Skecth Module G_B

- Download text dataset from [[Google Drive]](https://drive.google.com/open?id=1gjHR39deUSPChtRbKAD80waoQFTiXyMs) or [[Baidu Cloud]](https://pan.baidu.com/s/11LVKWAd6BCgWQqM6SZByEQ) to `../data/`

- Train G_B with default parameters
```
python trainSketchModule.py \
--text_path ../data/rawtext/yaheiB/train --text_datasize 708 \
--augment_text_path ../data/rawtext/augment --augment_text_datasize 5 \
--batchsize 16 --Btraining_num 12800 \
--save_GB_name ../save/GB.ckpt \
--gpu
```
or just modifying and running
```
sh ../script/launch_test.sh
```
Saved model can be found at `../save/`
- Use `--help` to view more training options
```
python trainSketchModule.py --help
```
  
### Training Structure Transfer G_S

- Download pre-trained G_B model from [[Google Drive]](https://drive.google.com/open?id=1gjHR39deUSPChtRbKAD80waoQFTiXyMs) or [[Baidu Cloud]](https://pan.baidu.com/s/11LVKWAd6BCgWQqM6SZByEQ) to `../save/` or use a saved model obtained by trainSketchModule.py
- Train G_S with default parameters
```
python trainStructureTransfer.py \
--style_name ../data/style/fire.png \
--batchsize 16 --Straining_num 2560 \
--step1_epochs 30 --step2_epochs 40 --step3_epochs 80 \
--scale_num 4 \
--Sanglejitter \
--save_path ../save --save_name fire \
--text_path ../data/rawtext/yaheiB/train --text_datasize 708 \
--load_GB_name ../save/GB-iccv.ckpt \
--gpu
```
or just modifying and running
```
sh ../script/launch_ShapeMGAN_structure.sh
```
Saved model can be found at `../save/`
- Use `--help` to view more training options
```
python trainStructureTransfer.py --help
```

### Training Texture Transfer G_T

- Download pre-trained G_S model from [[Google Drive]](https://drive.google.com/open?id=1gjHR39deUSPChtRbKAD80waoQFTiXyMs) or [[Baidu Cloud]](https://pan.baidu.com/s/11LVKWAd6BCgWQqM6SZByEQ) to `../save/` or use a saved model obtained by trainStructureTransfer.py
- Train G_T with default parameters
```
python trainTextureTransfer.py \
--style_name ../data/style/fire.png \
--batchsize 4 --Ttraining_num 800 \
--texture_step1_epochs 40 \
--Tanglejitter --style_loss \
--save_path ../save --save_name fire \
--text_path ../data/rawtext/yaheiB/train --text_datasize 708 \
--load_GS_name ../save/fire-GS.ckpt \
--gpu
```
or just modifying and running
```
sh ../script/launch_ShapeMGAN_texture.sh
```
Saved model can be found at `../save/`
- Use `--help` to view more training options
```
python trainTextureTransfer.py --help
```

### Contact

Shuai Yang

williamyang@pku.edu.cn
