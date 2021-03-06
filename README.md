# FISR
**This is the official repository of FISR (AAAI2020).**

We provide the training and test code along with the trained weights and the dataset (train+test) used for FISR. 
If you find this repository useful, please consider citing our paper.

**Reference**:  
> Soo Ye Kim*, Jihyong Oh*, and Munchurl Kim "FISR: Deep Joint Frame Interpolation and Super-Resolution with A Multi-scale Temporal Loss", *Thirty-Fourth AAAI Conference on Artificial Intelligence*, 2020. (* *equal contribution*)

### Requirements
Our code is implemented using Tensorflow, and was tested under the following setting:  
* Python 3.7 
* Tensorflow 1.13 
* CUDA 10.0  
* cuDNN 7.1.4  
* NVIDIA TITAN Xp GPU
* Windows 10

## Test code
### Quick Start
1. Download the source code in a directory of your choice **\<source_path\>**.
2. Download our 4K test dataset from [this link]( https://www.dropbox.com/s/101g9kdobgwl8x6/test.zip?dl=0) and unzip the 'test' folder in **\<source_path\>/data/test**, then you can get an input dataset (LR LFR), a flow data, a warped data and an output dataset (HR HFR) placed in **\<source_path\>/data/test/LR_LFR**, **\<source_path\>/data/test/flow** , **\<source_path\>/data/test/warped**  and **\<source_path\>/data/test/HR_HFR**, respectively. 
3. Download the pre-trained weights from [this link]( https://www.dropbox.com/s/usmoijfvnr3ok1q/FISRnet_exp1.zip?dl=0) and then unzip it to place in **\<source_path\>/checkpoint_dir/FISRnet_exp1**.
4. Place the both **HR_sample_5seq.mat** and **LR_sample_5seq.mat** files in **\<source_path\>/data/** for a faster loading (code issue cause of dependency in our code style). 
5. Run **main.py** with the following options in parse_args:  
**(i) For testing the our 4K test dataset input:**  
'--phase' as **'test'**, '--exp_num' as **1**, '--test_data_path' as **'./data/test/LR_LFR'**, '--test_flow_data_path' as **'./data/test/flow/LR_Surfing_SlamDunk_test_ss1.flo'**, '--test_warped_data_path' as **'./data/test/warped/LR_Surfing_SlamDunk_test_ss1_warp.mat'**, ‘--test_label_path’ as **'./data/test/HR_HFR**        
**(ii) For FISR testing on an one single folder, which contains a single scene (.png file input in YUV format) (will be updated as soon as possible):**  
'--phase' as **'FISR_for_video'**, ‘--exp_num' as **1**, ‘--frame_num' as **numbers of input frames you want to convert**, ‘--frame_folder_path’ as **folder path that you want to apply FISRnet**

### Description
* **Running the test option** will read the three input files, which are the LR LFR input sequences as **.png** files, the flows as pre-made **.flo** file and the warped frames as pre-made **.mat** file, respectively. Then it will save the predicted HR HFR results (FISR) in .png format in **\<source_path\>/test_img_dir/FISRnet_exp1**. The 70 YUV FISR results with 10 scenes will be converted into RGB files and **the performances (PSNR & SSIM on VFI-SR & SR separately) will be measured as in the paper**. **For faster testing** (to acquire performances only), you may comment the line for saving the predicted images as .png files.
* Please note that the uploaded version of our pre-trained FISRnet weights now yields **PSNR - VFI-SR: 37.86 [dB], SR: 48.07 [dB], SSIM – VFI-SR: 0.9743, SR: 0.9921**. 
* You can check our FISR results on our 4K test dataset by downloading [this link](https://www.dropbox.com/s/dym0kolu8niaty2/FISRnet_exp1.zip?dl=0) and then place them in **\<source_path\>/test_img_dir/FISRnet_exp1**.
* (will be updated as soon as possible) **Running the FISR_for_video option** will read all **.png** files in the designated folder and save the predicted FISR results in .png format in **\<source_path\>/test_img_dir/FISRnet_exp1/FISR_frames**. 
* (will be updated as soon as possible) If you wish to convert the produced .png files to a **.yuv video**, you could run the provided **PNGtoYUV.m** Matlab code, which would save the .yuv video in the same location as the .png files. **Please set the directory name accordingly.** (Encoding the raw .yuv video to a compressed video format such as .mp4 can be done using ffmpeg).
* **Due to GPU memory constraints**, the full 4K frame may fail to be tested at one go. The '--test_patch' option defines the number of patches (H, W) to divide the input frame (e.g. (1, 1) means the full 4K frame will be entered, (2, 2) means that it will be divided into 2x2 2K frame patches and processed serially). You may modify this variable so that the testing works with your GPU.
* **How to make flow and warped files by using PWC-Net:** you can download all the related tensorflow files from [this link](https://github.com/philferriere/tfoptflow) including weights and modified the folder name ‘tfoptflow’ as ‘FISR_tfoptflow’. Insert our modified versions for ‘test’ phase in ‘FISR_tfoptflow’ folder. You can make .flo flow file by using ‘FISR_pwcnet_predict_from_img_test.py’ and then get .mat warped file by using ‘FISR_warp_mat_with_flo.py’. 

## Training code
### Quick Start
1. Download the source code in a directory of your choice **\<source_path\>**.
2. Download our train dataset from [this link]( https://www.dropbox.com/s/n71hzqis6hpggcs/train.zip?dl=0) and unzip the 'train' folder in **\<source_path\>/data/train**, then you can get an input dataset (LR LFR), a two flow data (stride 1&2), a two warped data (stride 1&2) and an output dataset (HR HFR) placed in **\<source_path\>/data/train/LR_LFR**, **\<source_path\>/data/train/flow** , **\<source_path\>/data/train/warped**  and **\<source_path\>/data/train/HR_HFR**, respectively. 
 
3. Run **main.py** with the following options in parse_args:  
'--phase' as **'train'**, ‘--exp_num’, as **number for experiment, for example 7, which yields ‘FISRnet_exp7’** '--train_data_path' as **'./data/train/LR_LFR/LR_Surfing_SlamDunk_5seq.mat'**, '--train_flow_data_path' as **'./data/train/flow/LR_Surfing_SlamDunk_5seq_ss1.flo'**, '--train_flow_ss2_data_path' as **'./data/train/flow/LR_Surfing_SlamDunk_5seq_ss2.flo'**, '--train_warped_data_path' as **'./data/train/warped/LR_Surfing_SlamDunk_5seq_ss1_warp.mat'**, '--train_warped_ss2_data_path' as **'./data/train/warped/LR_Surfing_SlamDunk_5seq_ss2_warp.mat'**, '--train_label_path' as **'./data/train/HR_HFR/HR_Surfing_SlamDunk_5seq.mat'**

### Description
* **Running the train option** will train FISRnet and save the trained weights in **\<source_path\>/checkpoint_dir/FISRnet_exp~**.
* The trained model can be tested with **test** or **FISR_for_video** options.
* If you wish to compare with the provided weights, **change the '--exp_num' option** before training to another number than 1 to avoid overwriting the provided pre-trained weights so that the new weights are saved in a different folder (e.g. FISRnet_exp7).
* **The training process can be monitored using Tensorboard.** The log directory for using Tensorboard is **\<source_path\>/logdir/FISRnet_exp1**. Please note that the images are viewed in YUV itself (not RGB). 
* **How to make flow and warped files by using PWC-Net:** you can download all the related tensorflow files from [this link](https://github.com/philferriere/tfoptflow) including weights and modified the folder name ‘tfoptflow’ as ‘FISR_tfoptflow’. Insert our modified versions for ‘train’ phase in ‘FISR_tfoptflow’ folder. You can make .flo flow file by using ‘FISR_warp_mat_with_flo.py’ and then get .mat warped file by using ‘FISR_warp_mat_with_flo.py’. 


## Contact
Please contact us via email (jhoh94@kaist.ac.kr or sooyekim@kaist.ac.kr) for any problems regarding the released code.
