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
EDIT: random sampling leads to nonsense because of a mistak I made in the code. Random sampling in reality gives some structure but not a very nice structure: see small Pca Vae with no iftted variance random sampling
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
AN INTERESTING ISSUE IS THAT WHEN I TRY TO RESUME TRAINING WITH THE OLD WEIGHTS, IT SEEMS LIKE ALL TRAINING PROGRESS IS LOST; THE TRAINING RESTARTS WITH A SEEMINGLY HIGH ERROR. \ 
Results not great at all still

### PCA Vae Fitted Variance (small)
Maybe fitted variance would work on a smallish network: \
number of pca components: 500 \
intermediate dimension: 2048 \
number of intermediate layers: 1 \
latent dimension: 64 \
batch normalisation, relu activated \
no fancy initialisation \
error: MSE with fitted variance \
Unable to train this one properly due to NaN problems. Error seems to be decreasing normally, until it gets to about -650 to -700, then the KL-Divergence just explodes massively. So  I trained for 50 epochs before it exploded.\
The odd resuming training problem also recurred. I've got the training history evidence as well for that.\
See folder "PCAVaeFittedVarianceSmall" 

## Ideas for improvement

### U-net connections
As usual, have a 2-phase contracting and expanding convolutional network. The idea is to append the convolutional volumes from the contraction phase to the relevant volumes from the expansion phase

### KL Divergence epsilon trick
Add epsilon to the variance of q(z) to make sure that it doesn't blow up. So I no longer model the variance in log terms but in linear terms with an added epsilon (in the same way that brian keng models the output variance). All those trainings which use this trick will be marked as such.

In most cases, this was not needed in the end as good initialisation of weights and batch normalisation limited greatly the instability in training. I tried it for PCA Vae Fitted Variance (small) to try and remove the problems with instability, but it didn't help me at all, in fact I saw more nans. So I reverted those changes

### Questions to answer

## PCA fitted vs unfitted variance
Why does fitted variance give me worse results? Surely the results should be at least as good (as the network could easily just set all variances = 1/whatever constant my network is using right now).