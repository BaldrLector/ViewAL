# ViewAL: Active Learning with Viewpoint Entropy for Semantic Segmentation

This repository contains the implementation for the paper:

Yawar Siddiqui, Julien Valentin and Matthias Niessner, ["ViewAL: Active Learning with Viewpoint Entropy for Semantic Segmentation"](#) ([video](#))

![alt text]()

## Dependencies 

Check the `requirements.txt` for dependencies. 

## Arguments
```
train_active.py [-h] [--backbone {resnet,xception,drn,mobilenet}]
                       [--out-stride OUT_STRIDE]
                       [--dataset {scannet,scenenet-rgbd,matterport3d,scannet-sample}]
                       [--workers N] [--base-size BASE_SIZE]
                       [--sync-bn SYNC_BN] [--loss-type {ce,focal}]
                       [--epochs N] [--batch-size N] [--use-balanced-weights]
                       [--lr LR] [--lr-scheduler {step}]
                       [--optimizer {SGD,Adam}] [--step-size STEP_SIZE]
                       [--use-lr-scheduler] [--momentum M] [--weight-decay M]
                       [--nesterov] [--gpu-ids GPU_IDS] [--seed S]
                       [--checkname CHECKNAME] [--eval-interval EVAL_INTERVAL]
                       [--memory-hog]
                       [--max-iterations MAX_ITERATIONS]
                       [--active-selection-size ACTIVE_SELECTION_SIZE]
                       [--region-size REGION_SIZE]
                       [--region-selection-mode REGION_SELECTION_MODE]
                       [--view-entropy-mode {soft,vote,mc_dropout}]
                       [--active-selection-mode {random,viewentropy_region,voteentropy_soft,voteentropy_region,softmax_entropy,softmax_confidence,softmax_margin,coreset,voteentropy_max_repr,viewmc_kldiv_region,ceal}]
                       [--superpixel-dir SUPERPIXEL_DIR]
                       [--superpixel-coverage-dir SUPERPIXEL_COVERAGE_DIR]
                       [--superpixel-overlap SUPERPIXEL_OVERLAP]
                       [--start-entropy-threshold START_ENTROPY_THRESHOLD]
                       [--entropy-change-per-selection ENTROPY_CHANGE_PER_SELECTION]
```

#### Description
| Argument        | Description  |
| --------------- | ------------ |
| -h, --help | show this help message and exit|
| --backbone {resnet,xception,drn,mobilenet} | backbone name (default: mobilenet)|
| --out-stride OUT_STRIDE | network output stride (default: 16)|
| --dataset {scannet,scenenet-rgbd,matterport3d,scannet-sample} | dataset name (default: scannet-sample)|
| --workers N | dataloader threads|
| --base-size BASE_SIZE | base image size|
| --sync-bn SYNC_BN | whether to use sync bn (default: False)|
| --loss-type {ce,focal} | loss func type (default: ce)|
| --epochs N | number of epochs to train (default: auto)|
| --batch-size N | input batch size for training (default: auto)|
| --use-balanced-weights | whether to use balanced weights (default: True)|
| --lr LR | learning rate (default: auto)|
| --lr-scheduler {step} | lr scheduler mode: (default: step)|
| --optimizer {SGD,Adam}||
| --step-size STEP_SIZE | step size for lr-step-scheduler|
| --use-lr-scheduler| use learning rate scheduler|
| --momentum M |   momentum (default: 0.9)|
| --weight-decay M|   w-decay (default: 5e-4)|
| --nesterov | whether use nesterov (default: False)|
| --gpu-ids GPU_IDS|  use which gpu to train, must be a comma-separated list of integers only (default=0)|
| --seed S |   random seed (default: -1)|
| --checkname CHECKNAME | set the checkpoint name|
| --eval-interval EVAL_INTERVAL | evaluation interval (default: 5) - record metrics every Nth iteration|
| --memory-hog |   load the whole dataset in RAM if true|
| --max-iterations MAX_ITERATIONS | max active iterations|
| --active-selection-size ACTIVE_SELECTION_SIZE | active selection size|
| --region-size REGION_SIZE | window size for window region methods|
| --region-selection-mode REGION_SELECTION_MODE | use superpixels or windows as region selection mode|
| --view-entropy-mode {soft,vote,mc_dropout} | probability estimate = softmax or vote or mcdropout|
| --active-selection-mode | described below |
| --superpixel-dir SUPERPIXEL_DIR | directory for supepixel maps inside the dataset raw root|
| --superpixel-coverage-dir SUPERPIXEL_COVERAGE_DIR | directory for coverage maps inside the dataset raw root|
| --superpixel-overlap SUPERPIXEL_OVERLAP | superpixel overlap threshold|
| --start-entropy-threshold START_ENTROPY_THRESHOLD | ceal hyperparameter|
| --entropy-change-per-selection ENTROPY_CHANGE_PER_SELECTION | ceal hyperparameter|

#### Active Selection Mode

| Option        | Method  |
| --------------- | ------------ |
|random| RAND selection |
|voteentropy_max_repr| MAXRPR selection|
|voteentropy_soft| MCDR selection|
|voteentropy_region| RMCDR selection|
|softmax_entropy| ENT selection|
|softmax_confidence| CONF selection |
|softmax_margin| MAR selection |
|coreset| CSET selection |
|ceal| CEAL selection |
|viewmc_kldiv_region| ViewAL selection|

For more details, check out our paper.

#### Example commands

```
# View AL - sample dataset 

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname regional_viewmckldiv_spx_1_7x2_lr-0.0004_bs-6_ep-60_wb-0_lrs-1_240x320 --base-size 240,320 --max-iterations 7 --active-selection-size 2 --active-selection-mode viewmc_kldiv_region --region-selection-mode superpixel

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname regional_viewmckldiv_spx_1_7x1500_lr-0.0004_bs-6_ep-60_wb-0_lrs-1_240x320 --base-size 240,320 --max-iterations 7 --active-selection-size 1500 --active-selection-mode viewmc_kldiv_region --region-selection-mode superpixel

# Random

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname random_0_7x1500_lr-0.0004_bs-6_ep-60_wb-0_lrs-0_240x320 --base-size 240,320 --max-iterations 7 --active-selection-size 1500 --active-selection-mode random

# Softmax Entropy

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname softmax_entropy_0_7x1500_lr-0.0004_bs-6_ep-50_wb-0_lrs-1_240x320 --base-size 240,320 --max-iterations 7 --active-selection-size 1500 --active-selection-mode softmax_entropy

# Regional MCDR

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname regional_voteentropy_window_0_7x1500_lr-0.0004_bs-6_ep-60_wb-0_lrs-1_240x320 --base-size 240,320 --max-iterations 7 --active-selection-size 1500 --active-selection-mode voteentropy_region --region-selection-mode window

# CEAL 

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname ceal-0.00275_7x1500_lr-0.0005_bs-6_ep-50_wb-0_lrs-1_240x320 --max-iterations 7 --active-selection-size 1500 --base-size 240,320 --active-selection-mode ceal --start-entropy-threshold 0.0275 --entropy-change-per-selection 0.001815

# MCDR

python train_active.py --dataset scenenet-rgbd --workers 2 --epochs 50 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 35 --checkname mcdropoutentropy_0_7x1500_lr-0.0004_bs-6_ep-50_wb-0_lrs-1_240x320 --base-size 240,320 --max-iterations 7 --active-selection-size 1500 --active-selection-mode voteentropy_soft

# Full training 

python train.py --dataset scenenet-rgbd --workers 2 --epochs 70 --eval-interval 5 --batch-size=6 --lr 0.0004 --use-lr-scheduler --lr-scheduler step --step-size 40 --checkname full-run_0_lr-0.0004_bs-6_ep-60_wb-0_lrs-0_240x320 --base-size 240,320

```

## Files

Overall code structure is as follows: 

| File / Folder | Description |
| ------------- |-------------| 
| train_active.py | Training script for active learning methods | 
| train.py | Training script for full dataset training | 
| constants.py | Constants used across the code |
| argument_parser.py | Arguments parsing code |
| active_selection | Implementation of our method and other active learning methods for semantic segmentation |
| dataloader | Dataset classes |
| dataset | Train/test splits and raw files of datasets |
| model | DeeplabV3+ implementation (inspired from [here](https://github.com/jfzhang95/pytorch-deeplab-xception))|
| utils| Misc utils |

The datasets must follow the following structure

```
dataset # root dataset directory
├── dataset-name
    ├── raw
        ├── selections
            ├── color # rgb frames
            ├── label # ground truth maps
            ├── depth # depth maps
            ├── pose # camera extrinsics for each frame
            ├── info # camera intrinsics
            ├── superpixel # superpixel maps
            ├── coverage_superpixel # coverage maps
    ├── selections
        ├── seedset_0_frames.txt # seed set
        ├── train_frames.txt 
        ├── val_frames.txt
        ├── test_frames.txt
    ├── dataset.lmdb # rgb frames + labels in lmdb format
```

A small example dataset is provided with this repository in [`dataset/scannet-sample`](#).

## Data Generation

To use this repository datasets must be in the structure described in last section. For creating the lmdb database, seed set, train / test splits and superpixel maps check helper scripts in [`dataset/preprocessing-scripts`](#). We use [this SEEDS implementation](https://github.com/davidstutz/seeds-revised) for generating superpixels. Further, to generate superpixel coverage maps (`coverage_superpixel`) check [`utils/superpixel_projections.py`](#). 

## Citation

If you use this code, please cite the paper:

`
`