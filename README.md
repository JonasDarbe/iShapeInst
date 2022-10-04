<div align="center">
<h1>iShapeInst &#128640;</h1>
<span><font size="4", >iShapeInst: A novel and effective instance segmentation for irregularly shaped objects</font></span>
</br>
by
<br>
Yang Wang, Wanlin Zhou, QinWei Lv, Jiakai Zhou
<br>
College of mechanical and electrical engineering, Nanjing University of Aeronautics and Astronautics, Nanjing, China
</br>
</div>

## Abstract

In this paper, we propose an efficient, real-time, and fully convolutional framework instance segmentation for irregularly shaped objects, called iShapeInst. Irregularly shaped objects are widely existing in daily life and industrial scenes, and they are usually thin, curved, or with complex boundaries. The
prior instance segmentation methods rarely focus on irregularly shaped object, which drops dramatically for performance. This work improves the instance segmentation pipeline, proposing each pixel instead of the center of the object to predict kernel weight. Then, low-quality kernel weights of objects are filtered by employing metric learning and kernel fusion. Finally, instance masks are produced by convolution operation between kernel weights and mask feature. Specifically, at the training stage, iShapeInst applies regions instead of dense centers for instance to locate multiple kernel weights positions of the object. All kernel weights are dynamically optimized by metric learning and kernel fusion, which enforce intra-class compactness and inter-class discrepancy. At the inference stage, high-quality kernel weights of instances are generated by kernel fusion, and the prediction is obtained by convolution operation with mask feature map and Matrix NMS operation. Experimental
results show that iShapeInst can achieve state-of-the-art performance on six types of irregularly shaped object datasets, surpassing existing methods by 22.3% mmap and obtaining 31.5 FPS processing speed. We hope iShapeInst can serve as a strong baseline of instance segmentation for irregularly shaped objects. 

<div align="center">
<img src="resources/ishape.jpg">
</div>


## Models

All models are trained on MS-COCO [iShape dataset](https://ylshare.oss-cn-shanghai.aliyuncs.com/ishape_dataset.tar).

#### Models


| model | backbone | Antenna | Branch | Fence | Hanger | Log | Wire | Average | weights
| :---- | :------  | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Mask RCNN | Resnet-50 | 16.9 | 4.2 | 0.0 | 22.1 | 32.6 | 0.0 | 12.63  | [model](https://pan.baidu.com/s/1GNIPMSmkzWjHE2g8rmuKYA?pwd=hj7d) |
| PolarMask | Resnet-50 | 0.0 | 0.0 | 0.0 | 0.0 | 18.6 | 0.0 | 3.10  | model |
| SE | Resnet-50 | 38.3 | 0.0 | 0.0 | 49.8 | 20.9 | 0.0 | 18.17  | model |
| SOLOv2 | Resnet-50 | 6.6 | 27.5 | 0.0 | 28.8 | 22.2 | 0.0 | 14.07  | [model](https://pan.baidu.com/s/1a7qWdDptXh3QHnqdOqBrYw?pwd=h8g1) |
| DETR | Resnet-50 | 2.1 | 2.6 | 0.0 | 32.2 | 46.2 | 0.0 | 13.85  | model |
| SOLQ | Resnet-50 | 11.7 | 15.2 | 0.0 | 4.2 | 3.8 | 33.9 | 26.82 | [model](https://pan.baidu.com/s/1sZkCnNe_YFlIDpE9Lv3B9Q?pwd=vycz) |
| QueryInst | Resnet-50 | 1.1 | 4.8 | 0.0 | 17.6 | 26.1 | 0.2 | 8.30  | [model]() |
| SparseInst | Resnet-50d | 20.6 | 49.1 | 72.1 | 42.8 | 51.6 | 50.0 | 47.70  | [model](https://pan.baidu.com/s/1ayLOxE8yDOsnFaAasGzndg?pwd=aa3w ) |
| GMIS | Resnet-50 | 67.6 | 14.9 | 30.6 | 24.8 | 63.2 | 46.1 | 41.21  | model |
| ASIS | Resnet-50 | 88.5 | 24.6 | 60.4 | 57.4 | 69.4 | 77.3 | 62.93  | model |
| iShapeInst | DLA-34 | 84.1 | 86.8 | 94.2 | 81.7 | 78.2 | 86.4 | 85.23  | [model]() |


## Installation and Prerequisites

This project is built upon the excellent framework [detectron2](https://github.com/facebookreseach/detectron2), and you should install detectron2 first, please check [official installation guide](https://detectron2.readthedocs.io/en/latest/tutorials/install.html) for more details.

**Note:** we mainly use [v0.3](https://github.com/facebookresearch/detectron2/tree/v0.3) of detectron2 for experiments and evaluations. Besides, we also test our code on the newest version [v0.6](https://github.com/facebookresearch/detectron2/tree/v0.6). If you find some bugs or incompatibility problems of higher version of detectron2, please feel free to raise a issue!


Install the detectron2:

```bash
git clone https://github.com/facebookresearch/detectron2.git
# if you swith to a specific version, e.g., v0.3 (recommended)
git checkout tags/v0.3
# build detectron2
python setup.py build develop
```

## Getting Start

Before testing or training, you need to first modify lines 511-517 of `sparseinst/register_ishape_instance.py` to update the information of the dataset

### Testing iShapeInst

Before testing, you should specify the config file `<CONFIG>` and the model weights `<MODEL-PATH>`. In addition, you can change the input size by setting the `INPUT.MIN_SIZE_TEST` in both config file or commandline.

* [Performance Evaluation] To obtain the Inference Speed and evaluation results , *e.g.*, mask AP on iShape dataset, you can run:

```bash
python try_test_net.py --config-file <CONFIG>  MODEL.WEIGHTS <MODEL-PATH>
# example:
python try_test_net.py --config-file configs/sparse_inst_r50_giam.yaml MODEL.WEIGHTS model_final.pth
```

**Note:** 
* The `try_test_net.py` only supports **1 GPU** and **1 image per batch** for measuring inference speed.
* The inference time consists of the *pure forward time* and the *post-processing time*. While the evaluation processing, data loading, and pre-processing for wrappers (*e.g.*, ImageList) are not included.


### Training iShapeInst

To train the iShapeInst model on iShape dataset. If GPU memory is limited, it doesn't matter and you can reduce the batch size through `SOLVER.IMS_PER_BATCH` or reduce the input size. If you adjust the batch size, learning schedule should be adjusted according to the linear scaling rule.

```bash
python train_net.py --config-file <CONFIG> --num-gpus 1 
# example
python train_net.py --config-file configs/sparse_inst_r50_giam.yaml --num-gpus 1
```


## Acknowledgements


iShapeInst is based on [detectron2](https://github.com/facebookresearch/detectron2), [SparseInst](https://github.com/hustvl/SparseInst), [OneNet](https://github.com/PeizeSun/OneNet), [DETR](https://github.com/facebookresearch/detr), and [timm](https://github.com/rwightman/pytorch-image-models), and we sincerely thanks for their code and contribution to the community!


## Citing iShapeInst

If you find iShapeInst is useful in your research or applications, please consider giving us a star &#127775; and citing SparseInst by the following BibTeX entry.


## License

SparseInst is released under the [MIT Licence](LICENSE).
