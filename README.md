**Image classifier**
===================

This is a neural network starter for **fine-tuning InceptionV3 model with Keras, TensorFlow and your own data** to answer to image classification/recognition needs. It's ready to run on your own dataset without any modifications after installing dependencies. Lastly, you can easily customize the training constants to be coherent with your dataset/computer in top of each script.


> **Main features:**

> - Fine-tunes of the InceptionV3 model.
> - **Logger** and **multi-save model/weight** systems.
> - Can process **raw dataset or HDF5**.
> - Can **generate HDF5 dataset from raw** dataset.
> - Use **cross validation** to prevent overfitting.
> - Use **data augmentation** to prevent underfitting and help the model generalizing.
> - Use an **early stopping** system and **model checkpoints** on validation loss to always keep the best weight at each epochs.
> - Let you customize **how many data you want to load at the same time in your RAM** and still processed it all (really helpfull for hudge dataset).

----------

- [**Image classifier**](#image-classifier)
  * [Model results](#model-results)
  * [Installation](#installation)
      - [Install Anaconda](#install-anaconda)
      - [Set up environment](#set-up-environment)
      - [Install Tensorflow (tensorflow-gpu or tensorflow)](#install-tensorflow--tensorflow-gpu-or-tensorflow-)
      - [Install Keras](#install-keras)
      - [Install dependencies](#install-dependencies)
      - [Clone the repository](#clone-the-repository)
   * [Packages version used](#packages-version-used)
   * [Quick start](#quick-start)
      - [Training with a small dataset or really powerfull machin](#training-with-a-small-dataset-or-really-powerfull-machin)
      - [Training with a big dataset or a really weak machin](#training-with-a-big-dataset-or-a-really-weak-machin)
      - [Test your freshly computed model](#test-your-freshly-computed-model)
  * [Recommendations](#recommendations)
      - [Compute needed RAM/SWAP](#compute-needed-ram-swap)
      - [Your dataset quality will reflect your model performances](#your-dataset-quality-will-reflect-your-model-performances)
  * [Feedback](#feedback)

----------

Model results
-------------
I did my test on the great foods dataset [ETHZ-Food-101 data-set](https://www.vision.ee.ethz.ch/datasets_extra/food-101/). The first model can classify very efficiently carrot cake, hamburger, spagetti and sushi, great isn't it !?

| Model | Type of data | Classes | Data/classes | Accuracy | Top5 Accuracy | Training time |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 1 | Foods | 4 | 1000 | 0.9855 | - | 0:55:03 |
| 2 | Foods | 101 | 1000 | 0.7640 | 0.9394 | 17:04:17 |

These are validation results, the cross validation and data-augmentation were the key here to up model 2 results (without it I was around 0.50 but the training was like 10 hours). To give you an idea for the training time I run these with 32Go Ram and one Nvidia GTX970 (which is not really the greatest configuration for training neural networks).

----------

Installation
-------------

#### Install Anaconda
Follow installation process described in [Anaconda documentation](https://conda.io/docs/user-guide/install/index.html). I used the 4.2.12 version along with python 3.5.4.

You can check installation by running "conda list". Try to restart the terminal if it doesn't work.

#### Set up environment
```bash
conda create --name tensorflow python=3.5.4
source activate tensorflow #linux
activate tensorflow #windows
```

#### Install Tensorflow (tensorflow-gpu or tensorflow)
Follow installation process described in [tensorflow documentation](https://www.tensorflow.org/install/) depending on your operating system (I strongly recommand to use pip and anaconda installation).
If you have some issue later you probably mess with the installation of Cuda Toolkit or CuDNN (make sure you install the correct version 8.0 and 6.1 respectively).

If you have a GPU with [enought compute capability](https://www.tensorflow.org/install/install_windows#requirements_to_run_tensorflow_with_gpu_support) I strongly recommand to use tensorflow-gpu, your training will be a lot faster (like 10 times faster...).

#### Install Keras
```bash
pip install keras==2.0.8
```
If not working follow the [Keras documentation](https://keras.io/#installation).

#### Install dependencies
```bash
 conda install -c anaconda scikit-learn=0.19.0
 conda install -c menpo opencv3=3.1.0
 pip install numpy==1.13.3
 conda install -c anaconda h5py=2.7.0 
 conda install -c anaconda hdf5=1.8.15.1
```
If you have issue with opencv3 you may check this [thread](https://github.com/udacity/CarND-LaneLines-P1/issues/24).

#### Clone the repository
```bash
git clone https://github.com/plabadille/image_classifier
```
Then you should be ready to run !
In case of issue, please check your dependencies versions (it should work with newer but I have no way to know this). Commonly if you use gpu : **check your Cuda dependencies, version, installation**. If you're on **Windows check your opencv3 version**. If **you have installed tensorflow AND tensorflow-gpu... just choose one** ! Else, there's plenty of documentation on the web just waiting for you or feel free to ask if you need.

----------

Packages version used
-------------

 - Conda 4.2.12
 - Python 3.5.4
 - Keras 2.0.8
 - Tensorflow-gpu 1.3.0
 - Tensorflow-tensorboard 0.1.8
 - Scikit-learn 0.19.0
 - Opencv3 3.1.0
 - Numpy 1.13.3
 - H5py 2.7.0
 - Hdf5 1.8.1.15
 - Cuda Toolkit 8.0
 - cuDNN 6.1

----------


Quick start
-------------

### Training with a small dataset or really powerfull machin

 1) Copy paste your dataset in **image_classifier/data_dir**. Every sample should be separated inside this folder in folder named with the class_name. By example if you have a class Hamburger you can do : image_classifier/data_dir/hamburger/your files.

2) Feel free to customize the training constants in train.py :
```python
## Training const (line 62)
N_SPLITS = 4 #Cross validation const, the size of the validation set will be 1/n_splits
BATCH_SIZE = 32 #Number of samples that is going to be propagated through the network.
EPOCHS = 15 #Number of full training cycle on the training set. Note I use early stopping so it may never go up to the defined number of epochs.
BIG_EPOCHS = 3 #Number of time we are going to do a full train (each time from the best precedent one)
EARLY_SOPPING_PATIENCE = 2 #Number of time we are going to wait before stopping the training when the validation_loss doesn't decrease anymore during epochs (we always keep the best one anyway)
```
 
3) Run the training :
```bash
cd path/image_classifier
python train.py
```
It will process your raw dataset then finetune the InceptionV3 model with your own data. Logs will be in **image_classifier/logs/info_datetime.log** and model save (weight, class and json) in **image_classifier/save/image-classifier_datetime/*.**
Please look to the recommandations bellow to see how many RAM/SWAP you'll need depending on your dataset size. 

If you just want to try the run but you have a lot of data you can follow this procedure :
```bash
cd path/image_classifier
python reduce_dataset.py <new_data_directory_path> <max_samples_per_class>
python train.py <new_data_directory_path>
```

### Training with a big dataset or a really weak machin

**Please note the 3 first steps are needed only one time by dataset (if you want to update it you have to redo the procedure).**

 1) Copy paste your dataset in **image_classifier/data_dir**. Every sample should be separated inside this folder in folder named with the class_name. By example if you have a class Hamburger you can do : image_classifier/data_dir/hamburger/your files.

2) Serialize your dataset to hdf5 :
```bash
python serialized_dataset_to_hdf5.py <dataset_to_serialized_directory> <HDF5 file name>
```

Note : if you already have a hdf5 you can try to skip this steps but you will need to add it to **image_classifier/serialized_datasets**. If the shape of the numpies store in it are not right it will not work (**3 channels with channels last mode : (n,224,224,3)**. The first dataset have to be called **my_data** (X) and the second one **my_labels** (y). Finally you will need to **supplied a file called <your_hdf5_filename>.txt containing all your classes on one line separated by ','**.

3) Feel free to customize the training constants in train_with_hdf5.py :
```python
## Training const (line 62)
N_SPLITS = 4 #Cross validation const, the size of the validation set will be 1/n_splits
BATCH_SIZE = 32 #Number of samples that is going to be propagated through the network.
EPOCHS = 15 #Number of full training cycle on the training set. Note I use early stopping so it may never go up to the defined number of epochs.
BIG_EPOCHS = 3 #Number of time we are going to do a full train (each time from the best precedent one)
EARLY_SOPPING_PATIENCE = 2 #Number of time we are going to wait before stopping the training when the validation_loss doesn't decrease anymore during epochs (we always keep the best one anyway)
MAX_DATA_LOAD = 30000 #max data to load in memory at the same time (we still process all of it)
```
Please note that the **MAX_DATA_LOAD the key to avoid memory issue** (or using SWAP which will slow up like crazy your training. You can check bellow in recommandations how to know what to choose or test it the hard way.
 
4) Run the training :
```bash
python train_with_hdf5.py <hdf5_filename> #don't include the ext
```

### Test your freshly computed model
Paste your test pictures into **image_classifier/data_dir_test/*** and run :
```bash
python predict.py save/image_classifier_datetime_n 
#where n is the model number (0 is the first training, it is not pertinent. But each other one contain the best weight of each mega-epoch. Commonly you should use the last one)
```

----------


Recommendations
-------------

### Compute needed RAM/SWAP
I had to work a lot on memory leaks druing this project, I did a lot of optimization but I guess neural network with big dataset consum a lot of RAM (to load the data from disk). I advise you do to this little computation to help you anticipate how much RAM (enventually SWAP) you will need to run your network : `needed_ram = raw_dataset_size * 14`

**You do not have enought memory :** 

- If you want to use hdf5 (recommanded) : serialize_dataset_to_hdf5.py adding `swap = ram_missing * 2` will work fine, it will just take a bit more time (depending the swap/ram ratio) and then for the training you can fix the  `MAX_DATA_LOAD = ( SAMPLE_NUMBER / (DATA_SET_SIZE * 14) ) * RAM_AVAILABLE` (keep like 20% of ram free to be sure).

- If you want to use train.py : you could do add `swap = ram_missing * 2` but you should know that the training will be really slow (your gpu will never go really up because the data have to be read directly from disk). The best solution if you want to use this one is to reduce your dataset (explanation upper in the quick starter).

**Why this is taking so much RAM ?**

The first obvious reason is formating all the samples into a numpy array is 3 time heavier than your raw data weight.

Then I'm really not an expert but it have a lot to do with the ImageDataGenerator for data-augmentation which seem to use the total_numpy_dataset_weight * 3. One solution (it's what I did first before use data segmentation) is to not use data-augmentation (Then the ram used will be `raw_dataset_size * 3`).

I can't explain why 14 and not 9 for now, if you have some clue please tell me.


### Your dataset quality will reflect your model performances

Take your time for building your dataset in order to answer your need porperly. A small but hight quality (I don't mean 4k pictures but representative pictures for your given class) dataset will be a lot more efficient than a super-big one of medium-low quality. 
You can do everythings you want in your model development, if the dataset is not good, your model will be really bad.

----------

Feedback
-------------
Please feel free to reuse, ask me questions or give me tips/comments. I'm not an expert and would love to have some feed back and advise.

