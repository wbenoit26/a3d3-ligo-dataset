# a3d3-ligo-dataset
Challenge A3D3 dataset for detecting binary black hole (BBH) mergers in LIGO with Machine Learning

## Dataset
The dataset consists of 7 hdf5 files:

1. background.hdf5 - 100,000 noise samples generated by timesliding Hanford and Livingston data
2. waveforms-50-50.hdf5 - 50,000 BBH mergers with `m1=50` `m2=50`
3. waveforms-35-35.hdf5 - 50,000 BBH mergers with `m1=35` `m2=35`
4. waveforms-35-20.hdf5 - 50,000 BBH mergers with `m1=35` `m2=20`
5. waveforms-20-20.hdf5 - 50,000 BBH mergers with `m1=20` `m2=20`
6. waveforms-20-10.hdf5 - 50,000 BBH mergers with `m1=20` `m2=10`
7. waveforms-10-10.hdf5 - 50,000 BBH mergers with `m1=10` `m2=10`

Each hdf5 file contains an hdf5 dataset named `data` of shape `(N, 2, T)`, where `N` is the number of samples and `T` is the time
dimension of the data. The first channel of each sample corresponds to the Hanford data and the second channel corresponds to Livingston data.

```python
with h5py.File("background.hdf5") as f:
    data = f["data"][:]
    assert data.shape = (100_000, 2, T)

first_sample = data[0]
hanford = first_sample[0]
livingston = first_sample[1]
```

Every sample in each of the files is sampled at 2048 Hz. Besides for downsampling from 16KHz to 2KHz we do not do any pre-processing. For the waveform files, the coalescence time of the merger is placed 0.5 seconds away from the right edge. 

## Analyzing the Dataset
To make a submission, you must assign a scalar "detection statistic" to each sample. The larger the detection statistic, the more likely
your algorithm thinks a signal is present in the data. This detection statistic can have any range, and is not required to be between 0 and 1.

Submissions should be a single hdf5 file containing an hdf5 dataset for each file. The dataset name should be 
the same as the file to which it corresponds. The dataset should contain 1 dimensional numpy arrays containing your detection statistic for each sample. So, a submission might look like

```python
import numpy as np
import h5py
with h5py.File("dummy_submission.hdf5") as f:
    f.create_dataset("background", data=np.zeros((100_000)))
    f.create_dataset("waveforms-50-50", data=np.ones((50_000)))
    f.create_dataset("waveforms-35-35", data=np.ones((50_000)))
    f.create_dataset("waveforms-35-20", data=np.ones((50_000)))
    f.create_dataset("waveforms-20-20", data=np.ones((50_000)))
    f.create_dataset("waveforms-20-10", data=np.ones((50_000)))
    f.create_dataset("waveforms-10-10", data=np.ones((50_000)))
```

### A Note on Integrity
This challenge is meant to facilitate a fair comparison of machine learning ideas. Note that it would not be difficult to train your algorithm using solely the provided challenge data, and achieve a high score. This algorithm would likely be "overfit" and unlikely to perform well when it comes time to analyze real graviational wave data. So, we kindly ask participants not to use the challenge data at all during the training process.

## Dataset Generation
The dataset utilizes [`Aframe`](https://github.com/ml4gw/aframev2) for generating the segment of background strain and waveforms used to create
the challenge dataset. In addition, we provide the script used to generate the dataset from these files. To generate the data yourself, first
install `poetry` (if necessary) with 
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Next, install the environment by running
```bash
poetry install
```
from the root of the repository.

Inside the `run.sh` file, set `AFRAME_BASE_DIR` to your desired path for storing the data outputs.
Finally, you can execute data generation with
```bash
bash run.sh
```


## Aframe Submission
We provide a submission from the `Aframe` algorithm as a benchmark score to beat. Details of the algorithm can be found on the arxiv: https://arxiv.org/abs/2403.18661. We hope to learn from submissions that outperform this score!
