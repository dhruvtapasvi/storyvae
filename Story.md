### Improving the Performance of VAE on NORB

## About
This is a log of the steps as well as some of the reading I've taken to try and improve the performance of the VAE on NORB.

## Tried so far

# Basic Convolutional VAE
kl Loss Weight: 1.0 \
latent dimensions: 10 \
number of convolution downsizing: 6 \
intermediate dimension: 256 \
number of filters at first step: 16 \
loss type: binary cross entropy \
Performance in training was greatly improved when batch normalisation and He initialistion were included. The training is now much more stable and consistents converges to a similar value. \
Once these changes were implemented, some images are all right but many are not very good; some are completely unrecognisable. This holds for train reconstructions, test reconstructions, and random sampling. \
QUESTION: Perhaps adding more paramaters may help, or increasing capacity in some way. Not sure if the stopping condition was correctly met, perhaps training for more time could help. \
See folder "BasicConvolutionalVae"

# Fitted Variance Convolutional VAE
kl Loss Weight: N/A \
latent dimensions: 100 \
number of convolution downsizing: 6 \
intermediate dimension: 256 \
number of filters at first step: 16 \
loss type: mean squared error, fitted variance \
Used a fitted variance computed using the network as well, in addition to mean squared error loss \
Results are pretty bad all round \
See folder "FittedVarianceConvolutionalVae"

# Carrying out PCA
I carried out PCA using 100, 500, 1000 and 2000 components on NORB, and then viewed the reconstructions on the train, validation and test datasets \
500 seems like a good number of components to start off with trying to reproduce, there is some noticable corruption of images but it's much much less than the corruption that's currently occuring with the  VAE \
See folder "PCA/{#components}" for the pca reconstructions
QUESTION: Is 500 components an alright number to start off assuming?
