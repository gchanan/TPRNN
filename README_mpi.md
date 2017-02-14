# Instructions to run with MPI

## Installation
Install the latest [TorchMPI](https://github.com/facebookresearch/TorchMPI) on each machine.

If you are starting from my docker [image](https://hub.docker.com/r/nicolasvasilache/torchmpi-rdma-devel/) additionally intall ```cudnn``` and ```xlua``` on each machine.
For instance:
```
cat ${HOSTFILE} | xargs -n 1 -i ssh {} luarocks install cudnn
cat ${HOSTFILE} | xargs -n 1 -i ssh {} luarocks install xlua
```

To download the dataset on the local disk, you can run the followng once on each node:
```
luajit train_lm_bptt_mpi.lua -dset ptbw -model LSTM -nhid 100 -nlayer 1 -batchsize 64 -lr 0.5
luajit train_lm_bptt_mpi.lua -dset wikitext-103_large -model LSTM -nhid 256 -nlayer 1 -batchsize 16 -lr 0.5
```

## Runs
Small model, sync:
```
../TorchMPI-1/scripts/ompirun.sh -n 8 --npernode 4 --hostfile ${HOSTFILE} ../TorchMPI-1/scripts/wrap.sh luajit train_lm_bptt_mpi.lua -dset ptbw -model LSTM -nhid 100 -nlayer 1 -batchsize 64 -lr 0.5
```

Small model, async. Note async performance degrade significantly with 2 processes per GPU compared to sync
```
../TorchMPI-1/scripts/ompirun.sh -n 8 --npernode 4 --hostfile ${HOSTFILE} ../TorchMPI-1/scripts/wrap.sh luajit train_lm_bptt_mpi_async.lua -dset ptbw -model LSTM -nhid 100 -nlayer 1 -batchsize 64 -lr 0.5
```

Large model, sync:
```
../TorchMPI-1/scripts/ompirun.sh -n 8 --npernode 4 --hostfile ${HOSTFILE} luajit train_lm_bptt_mpi.lua -dset wikitext-103_large -model LSTM -nhid 256 -nlayer 1 -batchsize 16 -lr 0.5
```

Large model, async:
```
../TorchMPI-1/scripts/ompirun.sh -n 8 --npernode 4 --hostfile ${HOSTFILE} luajit train_lm_bptt_mpi_async.lua -dset wikitext-103_large -model LSTM -nhid 256 -nlayer 1 -batchsize 16 -lr 0.5
```
