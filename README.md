# Quick start
## Installation
1. Clone this repo, and we'll call the directory that you cloned multiview-multiperson-pose as ${POSE_ROOT}.
2. Install dependencies.

## Data preparation
### CMU Panoptic dataset
1. Download the dataset by following the instructions in [panoptic-toolbox](https://github.com/CMU-Perceptual-Computing-Lab/panoptic-toolbox) and extract them under `${POSE_ROOT}/data/panoptic_toolbox/data`.
- You can only download those sequences you need. You can also just download a subset of camera views by specifying the number of views (HD_Video_Number) and changing the camera order in `./scripts/getData.sh`. The sequences and camera views used in our project can be obtained from our paper.
- Note that we only use HD videos,  calibration data, and 3D Body Keypoint in the codes. You can comment out other irrelevant codes such as downloading 3D Face data in `./scripts/getData.sh`.
2. Download the pretrained backbone model from [pretrained backbone](https://1drv.ms/u/s!AtDMlSPvhLfYiGTxTE0OBazOXDYw?e=nBAdfJ) and place it here: `${POSE_ROOT}/models/pose_resnet50_panoptic.pth.tar` (ResNet-50 pretrained on COCO dataset and finetuned jointly on Panoptic dataset and MPII).

The directory tree should look like this:
```
${POSE_ROOT}
|-- models
|   |-- pose_resnet50_panoptic.pth.tar
|-- data
    |-- panoptic-toolbox
        |-- data
            |-- 16060224_haggling1
            |   |-- hdImgs
            |   |-- hdvideos
            |   |-- hdPose3d_stage1_coco19
            |   |-- calibration_160224_haggling1.json
            |-- 160226_haggling1  
            |-- ...
```

### Shelf/Campus datasets
1. Download the datasets from http://campar.in.tum.de/Chair/MultiHumanPose and extract them under `${POSE_ROOT}/data/Shelf` and `${POSE_ROOT}/data/CampusSeq1`, respectively.

2. We have transferred the camera parameters to our formats and you can download them from this repository. They lie in `${POSE_ROOT}/data/Shelf/` and `${POSE_ROOT}/data/CampusSeq1/`,  respectively.

3. Due to the limited and incomplete annotations of the two datasets, we use 3D human poses from panoptic dataset to train our models. It lies in `${POSE_ROOT}/data/panoptic_training_pose.pkl`.

4. For the sake of simplicity, we use predicted poses and corresponding values at the peaks to generate 2D heatmaps for this two small datasets in this repository.  The predicted poses can also download from the repository. They lie in `${POSE_ROOT}/data/Shelf/` and `${POSE_ROOT}/data/CampusSeq1/`,  respectively. You can also use the models trained on COCO dataset (like HigherHRNet) to generate 2D heatmaps directly.

The directory tree should look like this:
```
${POSE_ROOT}
|-- data
    |-- Shelf
    |   |-- Camera0
    |   |-- ...
    |   |-- Camera4
    |   |-- actorsGT.mat
    |   |-- calibration_shelf.json
    |   |-- pred_shelf_maskrcnn_hrnet_coco.pkl
    |-- CampusSeq1
    |   |-- Camera0
    |   |-- Camera1
    |   |-- Camera2
    |   |-- actorsGT.mat
    |   |-- calibration_campus.json
    |   |-- pred_campus_maskrcnn_hrnet_coco.pkl
    |-- panoptic_training_pose.pkl
```

## Training
### CMU Panoptic dataset

Train and validate on the five selected camera views. You can specify the GPU devices and batch size per GPU  in the config file. We trained our models on two GPUs.
```
python run/train_3d.py --cfg configs/panoptic/resnet50/prn64_cpn80x80x20_960x512_cam5.yaml
```
### Shelf/Campus datasets
```
python run/train_3d.py --cfg configs/shelf/prn64_cpn80x80x20.yaml
python run/train_3d.py --cfg configs/campus/prn64_cpn80x80x20.yaml
```

## Evaluation
### CMU Panoptic dataset

Evaluate the models. It will print evaluation results to the screen./
```
python test/evaluate.py --cfg configs/panoptic/resnet50/prn64_cpn80x80x20_960x512_cam5.yaml
```
### Shelf/Campus datasets

It will print the PCP results to the screen.
```
python test/evaluate.py --cfg configs/shelf/prn64_cpn80x80x20.yaml
python test/evaluate.py --cfg configs/campus/prn64_cpn80x80x20.yaml
```