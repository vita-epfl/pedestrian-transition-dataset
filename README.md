# TRANS: Benchmark for Pedestrian Stop & Go Forecasting
[![Python 3.8](https://img.shields.io/badge/python-3.8-blue.svg)](https://www.python.org/downloads/release/python-380//)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) <br>
This repository contains the code of *TRANS*, the benchmark introduced in our [paper](https://arxiv.org/abs/2203.02489) for explicitly studying the stop
and go behaviors of pedestrians in urban traffic. TRANS is built on top of several existing autonomous driving datasets annotated with walking behaviors
of pedestrians (see Table I), so that it includes transition
samples from diverse traffic scenarios with a unified interface

![ex1](docs/jaad_01.gif)

### Table of Contents
- [Installation](#installation)
- [Data preparation](#data-preparation)
- [Interface](#interface)
- [Statistics](#Statistics)
- [Further work](#further-work)
- [Citation](#citation)


## Installation

```
# To clone the repository using HTTPS
git clone https://github.com/vita-epfl/pedestrian-transition-dataset.git
```

The project is written and tested using python 3.8. The interface also require several external libraries.  All required packages can be found in `requirements.txt` and to install dependencies run:
```
pip install -r requirements.txt
```
Please ensure all expected modules are properly installed before using the interface.


## Data preparation
#### Supporting datasets

Currently we augment three existing self-driving datasets datasets:<br/>
* Joint Attention in Autonomous Driving([JAAD](http://data.nvision2.eecs.yorku.ca/JAAD_dataset/)) Dataset
* Pedestrian Intention Estimation([PIE](http://data.nvision2.eecs.yorku.ca/PIE_dataset/)) Dataset
* Trajectory Inference using Targeted Action priors Network([TITAN](https://usa.honda-ri.com/titan)) Dataset

#### Download
##### JAAD

- Download the videos and annotations from [official page](https://github.com/ykotseruba/JAAD). 
- Use the [scripts](https://github.com/ykotseruba/JAAD/blob/JAAD_2.0/split_clips_to_frames.sh) provided to extract images from videos.
- Use JAAD's [interface](https://github.com/ykotseruba/JAAD/blob/JAAD_2.0/jaad_data.py) to generate complete annotations in the form of python dictionary.
  More precisely use [`generate_database()`](https://github.com/ykotseruba/JAAD/blob/JAAD_2.0/jaad_data.py#L421) to obtain the all JAAD annotations in dictionary form as a pkl file.
  Please rename the .pkl file as `JAAD_DATA.pkl` and place it in `DATA/annotations/JAAD/anns`
- Train / val / test split: [link](https://github.com/ykotseruba/JAAD/tree/JAAD_2.0/split_ids).

##### PIE

- Download the videos and annotations from [offical page](https://github.com/aras62/PIE#interface). 
- Use the [scripts](https://github.com/aras62/PIE/blob/master/split_clips_to_frames.sh) provided to extract images from videos.
- Use PIE's [interface](https://github.com/aras62/PIE/blob/master/pie_data.py) to generate complete annotations in the form of python dictionary.
  In detail, please use [`generate_database()`](https://github.com/aras62/PIE/blob/master/pie_data.py#L441) to obtain the all JAAD annotations in dictionary form as a pkl file.
  Rename the .pkl file as `PIE_DATA.pkl` and place it in `DATA/annotations/PIE/anns`
- Train / val / test split: [link](https://github.com/aras62/PIE/blob/2256f96b8ab24d8407af34fb1f0b9a4714cd532e/pie_data.py#L84)

##### TITAN
To obtain the dataset, please refer to this [page]( https://usa.honda-ri.com/titan) and contact them directly.

After download the data, please place the images, annotations and video split ids in [DATA](https://github.com/DongxuGuo1997/TransNet/tree/main/DATA).
The expected result:
```
├── DATA/
│   ├── annotations/ 
│   │   ├── JAAD/ 
│   │   │   ├── anns/  
│   │   │   │   ├── JAAD_DATA.pkl # see Data-preparation-download-JAAD
│   │   │   └── splits/
│   │   │   │   ├── all_videos/
│   │   │   │   │   ├── train.txt
│   │   │   │   │   └── val.txt
│   │   │   │   │   └── test.txt
│   │   │   │   └── default/
│   │   │   │   │   ├── train.txt
│   │   │   │   │   └── val.txt
│   │   │   │   │   └── test.txt
│   │   │   │   └── high_visibility/
│   │   │   │   │   ├── train.txt
│   │   │   │   │   └── val.txt
│   │   │   │   │   └── test.txt
│   │   └── PIE/
│   │   │   ├── anns/ 
│   │   │   │   ├── PIE_DATA.pkl # see Data-preparation-download-PIE
│   │   └── TITAN/
│   │   │   ├── anns/
│   │   │   │   ├── clip_x.csv
│   │   │   └── splits/
│   │   │   │   │   ├── train_set.txt
│   │   │   │   │   └── val_set.txt
│   │   │   │   │   └── test_set.txt
│   └── images/
│   │   ├── JAAD/
│   │   │   ├── video_xxxx/ # 346 videos
│   │   └── PIE/
│   │   │   ├── set_0x/ # 6 sets
│   │   │   │   ├── video_xxxx/ 
│   │   └── TITAN/
│   │   │   ├── clip_xxx/  # 786 clips
└── (+ files and folders containing the raw data)
```
<b> Note </b>: The benchmark works  with arbitrary subsets of supported datasets when we give only the paths to desired ones.

## Interface  
At the heart of TRANS is the [`TransDataset`](https://github.com/vita-epfl/pedestrian-transition-dataset/blob/main/src/dataset/trans/data.py) class.
`TransDataset` generated transition samples from original annotations of JAAD, PIE and TITAN.
Using attributes of `TransDataset`, the user can conveniently extract the frame sequences related to the stop & go of pedestrians:<br>
The extracted samples each has an unique id specifying the source dataset, transition type, data split
and sample index. We provide customized  [`dataloader`](https://github.com/vita-epfl/pedestrian-transition-dataset/blob/main/src/dataset/loader.py) for loading samples according to the user's need. 
For basic usage please check the example in [<b>example.ipynb </b>](https://github.com/vita-epfl/pedestrian-transition-dataset/blob/main/example.ipynb). 
</b><br>

![ex2](docs/TITAN.gif)

## Statistics
All unique pedestrians in the original datasets can be categorized into Walk, Stand, Stop and Go. Overall we have 1,008 go events annd 1,138 stop events in TRANS. For more detailed analysis, please refer to the [paper](https://arxiv.org/abs/2203.02489). You can explore the statistics of different datastes using the verbose feature. 

## Further work
TRANS serves as the primary data source for the project "Pedestrian Stop and Go Forecasting" in [VITA](https://www.epfl.ch/labs/vita/). The interface is still under development. In future we may expect more relevant datasets to be integrated in this benchmark.
Please leave remarks if you encounter any problems using the interface or have suggestions for improving the usability.

## Citation

If you use this project in your research, please cite the corresponding paper:
```text
@article{guo2022pedestrian,
  title={Pedestrian Stop and Go Forecasting with Hybrid Feature Fusion},
  author={Guo, Dongxu and Mordan, Taylor and Alahi, Alexandre},
  journal={arXiv preprint arXiv:2203.02489},
  year={2022}
}
```