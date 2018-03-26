# Improving the Performance of VAE on NORB

## About
This is a log of the steps as well as some of the reading I've taken to try and improve the performance of the VAE on NORB.

## Tried so far

### Basic Convolutional VAE
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

### Fitted Variance Convolutional VAE
kl Loss Weight: N/A \
latent dimensions: 100 \
number of convolution downsizing: 6 \
intermediate dimension: 256 \
number of filters at first step: 16 \
loss type: mean squared error, fitted variance \
Used a fitted variance computed using the network as well, in addition to mean squared error loss \
Results are pretty bad all round \
See folder "FittedVarianceConvolutionalVae"

### Carrying out PCA
I carried out PCA using 100, 500, 1000 and 2000 components on NORB, and then viewed the reconstructions on the train, validation and test datasets \
500 seems like a good number of components to start off with trying to reproduce, there is some noticable corruption of images but it's much much less than the corruption that's currently occuring with the  VAE \
See folder "PCA/{#components}" for the pca reconstructions \
QUESTION: Is 500 components an alright number to start off assuming?

### PCA Vae (no fitted variance)
Using the PCA, I implemented deep dense VAE \
Number of pca components: 500 \
intermediate dimension: 512 \
number of intermediate layers: 4 \
latent dimension: 128 \
Batch normalised, and relu activated \
No fancy initialisation, but training was relatively stable anyway \
Error: MSE, not sure that BXE makes sense for the PCA components \
Train images look better than before, but test are pretty bad and random sampling leads to nonsense. \
QUESTIONS: Activation: none on the final layer, if pca components are all -1 to 1 then maybe tanh makes sense? Maybe training for more epochs? Maybe a higher capacity network? \
See folder "PCAVae"

### PCA Vae (no fitted variance), higher dimensions
I also expanded the depth of the VAE: \
Number of pca components: 500 \
intermediate dimension: 1024 \
number of intermediate layers: 5 \
latent dimension: 512 \
Batch normalised, and relu activated \
No fancy initialisation, but training was relatively stable anyway \
Error: MSE, not sure that BXE makes sense for the PCA components \
If anything results are worse, probably needs a lot more training to overfit the training set like I'm looking ot do. Maybe if I try a smaller Vae it might produce nicer results, anyway not sure how much all these intermediate layers help. \
See folder "PcaVaeLarge"

### PCA Vae (no fitted variance), lower dimensions
Trying out a small PCA VAE: \
Number of pca components: 500 \
intermediate dimension: 2048 \
number of intermediate layers: 1 \
latent dimension: 128 \
Batch normalised, and relu activated \
No fancy initialisation, but training was relatively stable anyway \
Error: MSE, not sure that BXE makes sense for the PCA components \
Results not too different to PCA VAE (not fitted variance) \
See folder "PCAVaeSmall"

### PCA Vae (no fitted variance), very small latent dimensions
Trying out an even smaller PCA VAE: \
Number of pca components: 500 \
intermediate dimension: 2048 \
number of intermediate layers: 1 \
latent dimension: 10 \
Batch normalised, and relu activated \
No fancy initialisation, but training was relatively stable anyway \
Error: MSE, not sure that BXE makes sense for the PCA components \
Results perhaps slightly worse than for 128 latent dimensions \
AN INTERESTING ISSUE IS THAT WHEN I TRY TO RESUME TRAINING WITH THE OLD WEIGHTS, IT SEEMS LIKE ALL TRAINING PROGRESS IS LOST; THE TRAINING RESTARTS WITH A SEEMINGLY HIGH ERROR. \ 
See folder "PCAVaeTiny"

### PCA Vae Fitted Variance
As above, except using fitted variance: \
number of pca components: 500 \
intermediate dimension: 1024 \
number of intermediate layers: 5 \
latent dimension: 128 \
batch normalisation, relu activated \
no fancy initialisation \
error: MSE with fitted variance \
See folder "PCAVaeFittedVariance" \
Basically quite similar to non fitted case except slightly worse results all around. Not sure why fitting the variance gives poorer result, maybe it was the effect of additional capacity with I didn't train for long enough. Perhaps just down to random variation.

### PCA Vae Fitted Variance (smallest)
Maybe fitted variance would work on a smaller network: \
number of pca components: 500 \
intermediate dimension: 2048 \
number of intermediate layers: 1 \
latent dimension: 10 \
batch normalisation, relu activated \
no fancy initialisation \
error: MSE with fitted variance \
See folder "PCAVaeFittedVarianceTiny" \
Results not great at all still

## Ideas for improvement

### U-net connections
As usual, have a 2-phase contracting and expanding convolutional network. The idea is to append the convolutional volumes from the contraction phase to the relevant volumes from the expansion phase

## Problems

### VAE has poor fit
Even training dataset is not well-reproduced.
Trying to improve capacity with various architectures

### Unstable training, often settled in suboptimal points, training loss bounced around
Use batch normalisation and better weights initialisation
Might want to try L2 regularisation as well
