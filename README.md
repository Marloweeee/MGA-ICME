
# Getting Started

### 1.Prepare the environment

``` python
python==3.8.10
icecream==2.1.3
matplotlib==3.7.5
mmcv_full==1.7.2
mmdet==2.11.0
einops==0.8.0
icecream==2.1.2
numpy==1.22.3
opencv_python==4.9.0.80
scipy==1.8.0
ftfy==6.1.1
timm==1.0.7
torch==2.0.0
torchvision==0.15.1
tqdm==4.65.2
transformers==4.39.3
```

The above is a tested environment. Other version of these packages may also be fine.

Please install mmdet from the source codes inside this repository (```./models/swin_model```).

### 2.Dataset preparation
We follow the data preparation of QRNet. Please read tips in ```./data``` and ```./ln_data```.

### 3.Checkpoint preparation
```
mkdir checkpoints
```
You can set the ```--bert_model``` to ```bert-base-uncased``` to download bert checkpoints online or put ```bert-base-uncased``` into ```checkpoints/``` manually.
The checkpoints of Swin-Transformer need to be manually download and put into ```./checkpoints```

## Training and Evaluation

### 1. Training

We present bash scripts for training  on refcoco as follows

```bash
dataset="unc"

python -m torch.distributed.launch --nproc_per_node=8 --use_env train.py --data_root ./ln_data/ \
 --batch_size 32 --lr 0.0001 --num_workers=8 \
 --output_dir ./outputs/$dataset \
 --dataset $dataset --max_query_len 20 \
 --aug_crop --aug_scale --aug_translate \
 --lr_drop 60 --epochs 90 --swin_checkpoint ./checkpoints/QRNet/unc_latest.pth

```

For training
```
conda activate conda_env

./train_refcoco.sh
```

It's similar to train the model on the other datasets. Differents is that on RefCOCOg, we recommend to set ```--max_query_len 40```, on RefCOCO+ We recommend to set ```--lr_drop 120  --epochs 180```.

### 2.Evaluation

We present bash scripts for testing  on refcoco as follows
```bash
dataset="unc"
splits="test"
max_query_len=20
weight_file="./outputs/$dataset/test_path"
output_dir="./outputs/$dataset_$split"

python -m torch.distributed.launch --nproc_per_node=8 --use_env eval.py \
                --batch_size 64 --data_root ./ln_data/ \
                --dataset $dataset --max_query_len $max_query_len --eval_set $split \
                --eval_model $weight_file --output_dir  "$output_dir"
```

For  evaluation
```
conda activate conda_env

./eval_refcoco.sh
```