

# pytorch-deepglr
A pytorch implementation of Deep Graph Laplacian Regularization for image denoising. Original work: [Zeng et al.](http://openaccess.thecvf.com/content_CVPRW_2019/papers/NTIRE/Zeng_Deep_Graph_Laplacian_Regularization_for_Robust_Denoising_of_Real_Images_CVPRW_2019_paper.pdf)
<p align="center">
  <img width="817" height="300" src="img/sample2.JPG" class="img-responsive">
</p>

Table of Contents
=================

   * [Installation](#installation)
   * [Basic usage](#basic-usage)
      * [Evaluate a trained model](#evaluate-a-trained-model)
         * [Evaluate DeepGLR](#evaluate-deepglr)
         * [Evaluate GLR](#evaluate-glr)
         * [NOTE](#note)
      * [Train a model](#train-a-model)
         * [Train a DeepGLR](#train-a-deepglr)
         * [Train a GLR](#train-a-glr)
      * [Remove noise of a single image using a trained DeepGLR](#remove-noise-of-a-single-image-using-a-trained-deepglr)
   * [Acknowledgments](#acknowledgments)
   
# Installation
1. Clone this [repository](https://github.com/huyvd7/pytorch-deepglr)

    ```git
    git clone https://github.com/huyvd7/pytorch-deepglr
    ```
2. Packages requirements: if you already had the same Pytorch version (pytorch==1.2.0, torchvision==0.4.0), remove 2 first lines in ```requirements.txt```. 

    ```python
    torch==1.2.0
    torchvision==0.4.0
    scipy==1.3.1
    matplotlib==3.1.2
    numpy==1.17.2
    scikit_image==0.16.2
    opencv-python
    ```

3. Install required packages listed in requirements.txt (with Python 3.7)

    ```python
    pip install -r requirements.txt
    ```     
      
4. If you have issues when installing PyTorch. Please follow their official installation guide [PyTorch](https://pytorch.org/get-started/previous-versions/). Please looking for this version ```pytorch==1.2.0 and torchvision==0.4.0```.

# Basic usage

We provide implementations for both DeepGLR and GLR since DeepGLR is a stacking version of GLRs. GLR is a smaller network and can be test more quickly, but it has poorer performance than DeepGLR.

## Evaluate a trained model

### Evaluate DeepGLR

    python validate_DGLR.py dataset/test/ -m model/deepglr.pretrained -w 324 -o ./
    
The above command resizes the input images located at ```dataset/test/``` to square images with size ```324 x 324```, then performs evaluation for the given trained DeepGLR ```model/deepglr.pretrained``` and saves outputs to ```./``` (current directory).


### Evaluate GLR

    python validate_GLR.py dataset/test/ -m model/glr.pretrained -w 324 -o ./
    
The above command resizes the input images located at ```dataset/test/``` to square images with size ```324 x 324```, then performs evaluation for the given trained GLR ```model/glr.pretrained``` and saves outputs to ```./``` (current directory).

      
### NOTE

<p align="center">
  <img width="1000" height="105" src="img/thumbnail.PNG">
</p>

The provided sample dataset in this repository is a resized version of RENOIR dataset (720x720 instead of 3000x3000). The original dataset is located at [Adrian Barbu's site](http://adrianbarburesearch.blogspot.com/p/renoir-dataset.html).

Since this is a resized dataset, **the evaluation results of DeepGLR are different from what were reported (MUCH HIGHER!!!)**. For the DeepGLR of this sample dataset, the evaluation scores are:

| Metric        | Train           | Test  |
| :-------------: |:-------------:| :-----:|
| SSIM | 0.900 | 0.887 |
| PSNR | 35.69 | 33.28 |

To reproduce the same results as written in the report, please replace the sample dataset in this repository with the original one. For your convinient, you can get the original from this [Google Drive mirror](https://drive.google.com/file/d/1gK611CnIC5PmUDLgCp8jVrEygn6VOytf/view?usp=sharing). This mirror will be deleted at the end of Dec 2019.

## Train a model
### Train a DeepGLR
    
    python train_DGLR.py dataset/train/ -n MODEL_NAME -d ./ -w 324 -e 200 -b 100 -l 2e-4 
    
The above command will train a new DeepGLR with these hyperparameters:

    Dataset: dataset/train/
    Output model name: MODEL_NAME (-n)
    Output directory: ./ (-d)
    Resize the given dataset to: 324x324 (-w)
    Epoch: 200 (-e)
    Batch size: 100 (-b)
    Learning rate: 2e-4 (-l)

**Note:** training a DeepGLR from scratch would require a lot of hyperparameter tuning depends on the dataset and also randomization. It is easier to train 4 separate single GLR first, then stack them manually (this is a future work, feel free to make a pull request!).

If you want to continue training an existing DeepGLR instead of training from scratch, you can add ```-m PATH_TO_EXISTED_MODEL```:

    python train_DGLR.py dataset/train/ -m model/deepglr.pretrained -n MODEL_NAME -d ./ -w 324 -e 200 -b 100 -l 2e-4 

### Train a GLR

    python train_GLR.py dataset/train/ -n MODEL_NAME -d ./ -w 324 -e 200 -b 100 -l 2e-4 
    
Same parameters as DeepGLR.


## Remove noise of a single image using a trained DeepGLR

    python denoise.py dataset/test/noisy/2_n.bmp -m model/deepglr.pretrained -w 324 -o OUTPUT_IMAGE.PNG

The above command will resize the ```dataset/test/noisy/2_n.bmp``` to ```324x324```, then denoise it using a trained DeepGLR ```model/deepglr.pretrained``` and save the result at ```OUTPUT_IMAGE.PNG```.

# Acknowledgments
Most of these works are done on Google Colaboratory. Thanks Google for the free GPUs. 
