# Adv-attack-and-defense-on-driving-model

**This code is based on the paper [An Analysis of Adversarial Attacks and Defenses on Autonomous Driving Models](https://arxiv.org/abs/2002.02175)** [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.3580012.svg)](https://doi.org/10.5281/zenodo.3580012)
## Citation for the above paper
```
@article{deng2020analysis,
  author    = {Deng, Yao and Zheng, Xi and Zhang, Tianyi and Chen, Chen and Lou, Guannan and Kim, Miryung},
  title     = {An Analysis of Adversarial Attacks and Defenses on Autonomous Driving Models},
  journal   = {CoRR},
  volume    = {abs/2002.02175},
  year      = {2020}
}
```

```
Yao Deng, Xi Zheng, Tianyi Zhang, Chen Chen, Guannan Lou, and Miryung Kim. An analysis of adversarial attacks and defenses  on autonomous driving models. CoRR, abs/2002.02175, 2020.
```



<!-- Results on CNN/DailyMail (20/8/2019):


<table class="tg">
  <tr>
    <th class="tg-0pky">Models</th>
    <th class="tg-0pky">ROUGE-1</th>
    <th class="tg-0pky">ROUGE-2</th>
    <th class="tg-0pky">ROUGE-L</th>
  </tr>
  <tr>
    <td class="tg-c3ow" colspan="4">Extractive</td>
  </tr>
  <tr>
    <td class="tg-0pky">TransformerExt</td>
    <td class="tg-0pky">40.90</td>
    <td class="tg-0pky">18.02</td>
    <td class="tg-0pky">37.17</td>
  </tr>
  <tr>
    <td class="tg-0pky">BertSumExt</td>
    <td class="tg-0pky">43.23</td>
    <td class="tg-0pky">20.24</td>
    <td class="tg-0pky">39.63</td>
  </tr>
  <tr>
    <td class="tg-0pky">BertSumExt (large)</td>
    <td class="tg-0pky">43.85</td>
    <td class="tg-0pky">20.34</td>
    <td class="tg-0pky">39.90</td>
  </tr>
  <tr>
    <td class="tg-baqh" colspan="4">Abstractive</td>
  </tr>
  <tr>
    <td class="tg-0lax">TransformerAbs</td>
    <td class="tg-0lax">40.21</td>
    <td class="tg-0lax">17.76</td>
    <td class="tg-0lax">37.09</td>
  </tr>
  <tr>
    <td class="tg-0lax">BertSumAbs</td>
    <td class="tg-0lax">41.72</td>
    <td class="tg-0lax">19.39</td>
    <td class="tg-0lax">38.76</td>
  </tr>
  <tr>
    <td class="tg-0lax">BertSumExtAbs</td>
    <td class="tg-0lax">42.13</td>
    <td class="tg-0lax">19.60</td>
    <td class="tg-0lax">39.18</td>
  </tr>
</table> -->

**Python version**: This code is in Python3.7

**Package Requirements**: torch==1.1.0, torchvision==0.2.2,numpy, matplotlib, cv2, scipy

**Experiment Enviroment**: We perform the experiment in the simulation environment with `Intel i7-8700 3.2GHz, 32GB of memory`, and a `NVIDIA RTX 2080Ti GPU`.   

## Dataset
The dataset used in this project is [Udacity](https://github.com/udacity/self-driving-car/tree/master/datasets/CH2), which contains real-word condition images collected by a front camera installed in a vehicle. The training and testing dataset contains 33805 and 5614 frames, speriately. The steer angle of each frame is converted from a degree to a calue in the range (-1,1)
### Preprocessing
Download and unzip the `stories` directories from [here](https://github.com/udacity/self-driving-car/tree/master/datasets/CH2#ch2_001) for both training (`HMB_1, HMB_2, HMB_4, HMB_5, HMB_6`) and testing set (`HMB_3`), rename the `HMB_3` directory as `testing`, and put the whole dataset (both training and testing dataset) into a new directory: `../udacity-data`. The preprocessing method is also included in this link.


## Step1: Training driving models
### Epoch
```
python train.py --model_name baseline --root_dir ../udacity-data --batch_size 32 --epochs 15 --train 1 --lr 0.0001
```

### Nvidia
```
python train.py --model_name nvidia --root_dir ../udacity-data --batch_size 32 --epochs 15 --train 1 --lr 0.0001
```

### Vgg16
```
python train.py --model_name vgg16 --root_dir ../udacity-data --batch_size 32 --epochs 15 --train 1 --lr 0.0001
```

* `--model` can be {`baseline, nvidia, vgg16`} refers to three driving models: `Epoch`, `Nvidia` and `Vgg16`.
* `--train` is the mode of training, which can be {`0`,`1`,`2`}, where `0` is the `test mode` (only test the result of the saved model, without training a new model), `1` is the `train mode` (new model file will be generated and old model will be replaced), `2` is the `continue mode` (the training continue with the old model)


## Step2: Perform adversrial attack

At first creat a new dir `../udacity-data/adv_data` to store the generated adversial examples.

### Attacks

* **FGSM**: The implementation of this attack method is in the file `./fgsm_attack.py`

* **OPT**: The implementation of this attack method is in the file `./optmization_attack.py`

* **OPT_UNI**: The implementation of this attack method is in the file `./optmization_universal_attack.py`

* **AdvGAN**: The implementation of this attack method is in the file `./advGAN_attack.py`, which is adapted from [advGAN_pytorch](https://github.com/mathcbc/advGAN_pytorch)

* **AdvGAN_UNI**: The implementation of this attack method is in the file `./advGAN_attack.py`

### Experiments
* white-box attacks
  ```
  python experiment.py --experiment 1
  ```

* black-box attacks
  ```
  python experiment.py --experiment 2
  ```


## Step3: Perform adversrial defence

At first, generate a new directory `../adv_training_models` to save the model generated by `adv_training` and `distillation` 

### Adversial Training
```
python adv_training.py
```
* The implementation of this defence method is in the file `adv_training.py`
* The experiment result will show on the terminal

### Distillation
```
python distillation.py
```
* The implementation of this defence method is in the file `distillation.py`
* The experiment result will show on the terminal

### Feature Squeezing 
```
python feature_squeezing.py -- threshold 0.01
```
* The implementation of this defence method is in the file `feature_squeezing.py`
* The experiment result will show on the terminal



[Here](https://youtu.be/jyT1mYVi6XA) is an attack demo video of `opt_uni` and `advGAN` on `Epoch` model.

