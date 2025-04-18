# ASV-anti-spoofing
This repository focuses on building and evaluating models to detect spoofed audio in speaker verification systems.
We selected three models to experiment with for this task:
1. VGG + SincNet-based Model
Based on the paper "Detecting Spoofing Attacks Using VGG and SincNet: BUT-Omilia Submission to ASVspoof 2019 Challenge" by Hossein Zeinali, Themos Stafylakis, Georgia Athanasopoulou, Johan Rohdin, Ioannis Gkinis, Lukáš Burget, and Jan "Honza" Černocký.
Detailed documentation is available at: 
ASV-anti-spoofing/Documentations/VGG+SincNet-based-Model
2. Res2Net-based Model
Based on the paper "Replay and Synthetic Speech Detection with Res2Net Architecture" by Xu Li, Na Li, Chao Weng, Xunying Liu, Dan Su, Dong Yu, and Helen Meng.
Detailed documentation is available at: 
ASV-anti-spoofing/Documentations/Res2Net
3.  Light Convolutional GRU-RNN Feature Extractor
Detailed documentation is available at: 
ASV-anti-spoofing/Documentations/Light-Conv-GNU-RNN
Based on the paper:
"A Light Convolutional GRU-RNN Deep Feature Extractor for ASV Spoofing Detection" by Alejandro Gomez-Alanis, Antonio M. Peinado, Jose A. Gonzalez, and Angel M. Gomez.



## Model Implementation
In this repository, we have implemented the second model: "Replay and Synthetic Speech Detection with Res2Net Architecture".

1. ResNet blocks
<div  align="center"> <img src="./result_table_figure/resnet-blocks.png" width = "400" height = "600" align=center /></div>

2. Overall model architecture
![](./result_table_figure/overall-architecture.png)

## Main Results
1. System performance on the ASVspoof2019 PA and LA dataset. (The input features for PA and LA are Spec and LFCC, respectively.)
![](./result_table_figure/sys-performance-diff-architecture.png)

2. System performance on the ASVspoof2019 PA (left) and LA (right) of SE-Res2Net50 with different acoustic features.
<div  align="center"><img src="./result_table_figure/sys-performance-pa-diff-acoustic-feats.png" width = "390" height = "195" align=center /><img src="./result_table_figure/sys-performance-la-diff-acoustic-feats.png" width = "390" height = "195" align=center /></div>


## Dependencies

1. Python and packages

    This code was tested on Python 3.7 with PyTorch 1.6.0.
    Other packages can be installed by:

    ```bash
    pip install -r requirements.txt
    ```

2. Kaldi

   This work used Kaldi to extract features, you need to install [Kaldi](https://github.com/kaldi-asr/kaldi) before running our scripts.

3. MATLAB

   The LFCC feature adopted in this work is extracted via the MALTAB codes privided by ASVspoof2019 orgnizers.

## Dataset
   This work is conducted on [ASVspoof2019 Dataset](https://arxiv.org/pdf/1904.05441.pdf), which can be downloaded via https://datashare.ed.ac.uk/handle/10283/3336. It consists of two subsets, i.e. physical access (PA) for replay attacks and logical access (LA) for synthetic speech attacks.

## Start Your Project
   This repository mainly consists of two parts: (i) feature extraction and (ii) system training and evaluation.

### Feature extraction
   Three features are adopted in this repo, i.e. Spec, LFCC and CQT. The top script for feature extraction is `extract_feats.sh`, where the first step (Stage 0) is required to prepare dataset before feature extraction. It also provides feature extraction for Spec (Stage 1) and CQT (Stage 2), while for LFCC extraction, you need to run the `./baseline/write_feature_kaldi_PA_LFCC.sh` and `./baseline/write_feature_kaldi_LA_LFCC.sh` scripts. All features are required to be truncated by the Stage 4 in `extract_feats.sh`.

   Given your dataset directory in `extract_feats.sh`, you can run any stage (e.g. NUM) in the `extract_feats.sh` by
   ```bash
   ./extract_feats.sh --stage NUM
   ```
   For LFCC extraction, you need to run
   ```bash
   ./baseline/write_feature_kaldi_LA_LFCC.sh
   ./baseline/write_feature_kaldi_PA_LFCC.sh
   ```

### System training and evaluation
   This repo supports different system architectures, as configured in the `conf/training_mdl` directory. You can specify the system architecture, acoustic features in `start.sh`, then run the codes below to train and evaluate your models.
   ```bash
   ./start.sh
   ```
   Remember to rename your `runid` in `start.sh` to differentiate each configuration.
   From our experiments after ICASSP 2021 submission, we observe that SERes2Net50 configured with `14w_8s` and `26w_8s` can achieve slightly better performance.

   For evaluating systems, you can either use the Kaldi command `compute-eer` with the resulting `*.eer` file to compute system EER, e.g.
   ```bash
   . ./path.sh
   compute-eer NameofScoringFile.txt.eer
   ```
   or use the ASVspoof2019 official script `scoring/evaluate_tDCF_asvspoof19.py` with the resulting `*.txt` file to compute both system EER and t-DCF, e.g. on the LA evalation set, you need to run
   ```bash
   python scoring/evaluate_tDCF_asvspoof19.py scoring/la_asv_scores/ASVspoof2019.LA.asv.eval.gi.trl.scores.txt NameofScoringFile.txt
   ```
