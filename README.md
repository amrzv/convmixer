# Patches Are All You Need?

This repository contains an implementation of ConvMixer for the ICLR 2022 submission ``Patches Are All You Need?''.

The most important code is in `convmixer.py`. We trained ConvMixers using the `timm` framework, which we copied from [here](http://github.com/rwightman/pytorch-image-models).

Inside `pytorch-image-models`, we have made the following modifications. (Though one could look at the diff, we think it is convenient to summarize them here.)

- Added ConvMixers
  - timm/models/convmixer.py
  - timm/models/__init__.py
- Added "OneCycle" LR Schedule
  - timm/scheduler/onecycle_lr.py
  - timm/scheduler/scheduler.py
  - timm/scheduler/scheduler_factory.py
  - timm/scheduler/__init__.py
  - added two lines in train.py to support this LR schedule

We are confident that the use of the OneCycle schedule here is not critical, and one could likely just as well
train ConvMixers with the built-in cosine schedule.

If you had a node with 10 GPUs, you could train a ConvMixer-1536/20 as follows (these are exactly the settings we used):

```
sh distributed_train.sh 10 [/path/to/ImageNet1k] --train-split [your_train_dir] --val-split [your_val_dir] --model convmixer_1536_20 -b 64 -j 10 --opt adamw --epochs 150 --sched onecycle --amp --input-size 3 224 224 --lr 0.01 --aa rand-m9-mstd0.5-inc1 --cutmix 0.5 --mixup 0.5 --reprob 0.25 --remode pixel --num-classes 1000 --warmup-epochs 0 --opt-eps=1e-3 --clip-grad 1.0
```
