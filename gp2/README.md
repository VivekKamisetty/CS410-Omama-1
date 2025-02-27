# GP2 Example

This example demonstrates how to uee the GP2 module for optimizing any classifier. In this case, we'll be using the KUNetPlus2D classifier.

## Dependencies

Install the required dependencies by createing a new Anaconda environment using the `GP2.yml` file
in the root directory of the project:
```bash
conda env create -f GP2.yml
```
Activate the environment using:
```bash
conda activate GP2
```

## Example Code


```python
import sys
import numpy as np

sys.path.insert(0, '../..')

import gp2
from gp2 import Runner
```
# To get info about the model, run the following:
``` python
help(gp2.KUNetPlus2D)
```
# To use the Keras UnetPlus2D Unet, specify it as the classifier argument to the Runner instance when creating it:
``` python
R = Runner(
    verbose=True,
    classifier='kunetplus2d',
    discriminator='cnn',
    filter_num=[16, 32, 64, 128, 256, 512, 1024],
    stack_num_down=3,
    stack_num_up=3,
    activation='ReLU',
    output_activation='Sigmoid',
    batch_norm=True,
    pool=True,
    unpool=True,
    deep_supervision=False,
    weights=None,
    freeze_backbone=True,
    freeze_batch_norm=True,
    optimizer=None,
    loss=gp2.Util.hybrid_loss,
    metric=None
)
```

# Load data
``` python
images = np.load('/path/to/images.npy')
masks = np.load('/path/to/masks.npy')
```

# Specify weights for the distribution of data into train, test, and val datasets for datasets A and B
``` python
weights = {
    'A': 0.5,
    'A_train': 0.1,
    'A_val': 0.3,
    'A_test': 0.6,
    'B': 0.3,
    'B_train': 0.7,
    'B_val': 0.1,
    'B_test': 0.2,
    'Z': 0.2
}
```


# To run training, use the Runner's run method, passing in the images, masks, weights, and the number of training loops to run for:
``` python
R.run(images=images, masks=masks, weights=weights, runs=7)
```

# Use the Runner's plot method to easily visualize results
``` python
R.plot()
```

# Additional Information
In this section, we provide an overview of various optimizers, loss functions, and metric functions that you can use when working with the GP2 module. When training your model, it's essential to choose the appropriate optimizer, loss function, and metric function for your specific task. We'll briefly describe each option, which should help you make an informed decision when configuring your model.

Keep in mind that different tasks and datasets might require different combinations of optimizers, loss functions, and metric functions for optimal results. We encourage you to experiment with various options to find the best configuration for your specific problem. Additionally, consider using learning rate schedules and data augmentation techniques to further improve your model's performance.
## Optimizer:
    - Gradient Descent (SGD) - The most basic optimizer, which updates the model's 
            parameters in the direction of the negative gradient of the loss function 
            with respect to the parameters.
            In Keras: tf.keras.optimizers.SGD
    - Momentum - A variation of gradient descent that helps accelerate convergence 
            by using an exponential moving average of the gradients.
            In Keras: tf.keras.optimizers.SGD with the momentum parameter set to a non-zero value.
    - Nesterov Accelerated Gradient (NAG) - A modification of the momentum optimizer 
            that improves convergence by considering the future position of the gradients.
            In Keras: tf.keras.optimizers.SGD with the momentum and nesterov parameters 
            set to non-zero values.
    - Adagrad (Adaptive Gradient Algorithm) - Adapts the learning rate for each 
            parameter individually based on the history of gradients, which can be 
            particularly useful for sparse data.
            In Keras: tf.keras.optimizers.Adagrad
    - RMSprop (Root Mean Square Propagation) - Adapts the learning rate for each 
            parameter individually based on an exponential moving average of the squared gradients.
            In Keras: tf.keras.optimizers.RMSprop
    - Adadelta - A variation of Adagrad that seeks to address its diminishing 
            learning rates by accumulating a window of past gradients.
            In Keras: tf.keras.optimizers.Adadelta
    - Adam (Adaptive Moment Estimation) - A popular choice that combines the 
            concepts of RMSprop and momentum to adapt the learning rate for each 
            parameter individually.
            In Keras: tf.keras.optimizers.Adam
    - Adamax - A variation of Adam that uses the infinity norm of the gradients 
            to update the learning rate for each parameter.
            In Keras: tf.keras.optimizers.Adamax
    - Nadam (Nesterov-accelerated Adaptive Moment Estimation) - Combines the 
            concepts of Adam and Nesterov momentum for adaptive learning rates.
            In Keras: tf.keras.optimizers.Nadam
    - FTRL (Follow The Regularized Leader) - An optimizer designed for large-scale 
            linear models with support for L1 and L2 regularization.
            In Keras: tf.keras.optimizers.Ftrl

## Loss functions:
    - Cross-Entropy Loss - A widely used loss function for classification tasks, 
            including image segmentation. It measures the dissimilarity between 
            the predicted probability distribution and the true distribution.
    - Binary Cross-Entropy Loss - Used for binary segmentation problems, where 
            each pixel is classified into one of two classes. It measures the 
            dissimilarity between the predicted probability and the true label 
            for each pixel. This is a special case of Cross-Entropy Loss when 
            there are only two classes.
    - Categorical Cross-Entropy Loss - Used for multi-class segmentation problems, 
            where each pixel is classified into one of multiple classes. Similar to 
            Cross-Entropy Loss, it measures the dissimilarity between the predicted 
            probability distribution and the true distribution, but it is applied to a 
            one-hot encoded representation of the labels.
    - Dice Loss - A loss function specifically designed for segmentation tasks,
            focusing on overlapping regions between the predicted and ground 
            truth masks.
    - Jaccard/Intersection over Union (IoU) Loss - Another loss function tailored 
            for segmentation tasks, which penalizes based on the area of overlap 
            between the predicted and ground truth masks.
    - Combined Loss - A combination of different loss functions 
            (e.g., Cross-Entropy Loss + Dice Loss) can sometimes improve performance 
            by leveraging the strengths of multiple loss functions.

## Metric functions:
    - IoU (Intersection over Union) - A popular metric to measure the overlap 
            between the predicted and ground truth masks. A higher IoU indicates 
            better segmentation performance.
    - Dice Coefficient - Similar to IoU, it measures the overlap between the 
            predicted and ground truth masks, with a range of 0 to 1 (higher values 
            indicate better performance).
    - Pixel Accuracy - The ratio of correctly labeled pixels to the total number 
            of pixels in the image.
    - Mean Intersection over Union (mIoU) - The average IoU over all classes, 
            taking into account class imbalances.

Remember to experiment with different combinations of optimizers, loss functions, 
and metric functions to find the best configuration for your specific task. 
You may also consider using learning rate schedules and data augmentation techniques 
to further improve your model's performance.

