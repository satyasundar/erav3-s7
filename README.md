# Optimised MNIST Model- Progressively

## Objective

We will be making a model for MNIST dataset with below criteria.

- Parameter size will be less than **8000**
- Test Accuracy will be above **99.4%** consistently
- This should be achived under **15** epochs

## Approach

Each file has got complete log and detailed analysis.

- Model-1 : This is a BASE model.

  - Here we will conclude with network architecture.
    - Number of layers.
    - MaxPooling position.
    - Transition Layers
    - GAP Layer

- Model-2 : This is a INTERIM model.

  - Here we will conclude with network architecture.
    - Batch Normalisation
    - Dropout
    - Image Augmentation

- Model-3 : This is a **FINAL** model.

  - Here we finally tweak the model with below techniques to achive the objective
    - StepLR Scheduler
    - ADAM Optimiser

## MODEL - 1 : BASE [[model_1_base.ipynb]](./model_1_base.ipynb)

### TARGET

- Carefully think and plan the Model architecture

- The requirement is to achive high accuracy with less parameters within 15 epoch

- Approach is to after 2 layers of convolution when RF is 5, we will do MaxPooling. This is MNIST dataset, hence we can find edges and gradients at RF 5.

- Transition block of 1x1 convolution follows Maxpooling to summerize the channels from depth perspective as well as reduce the channel numbers.

- Then we will continue 4 convolution layers of 3x3 kernel until we reach GAP layer.

- GAP layer summerizes each feature channel as 1x1

- There is one last layer from GAP layer output channels to 10

### RESULT

- Parameters : **7704**

- Best Train Accuracy : **98.63%**

- Best Test Accuracy : **98.61%**

### Analysis

- **Model Architecture**

  - **Channels** : 1 → 8 → 16 → MaxPool → 16 → (Transition) → 8 → 12 → 12 → 16 → 16 → GAP → 16 → Transition → 10

  - **Receptive Field**

    | Block               | Layer | Input Size | Kernel x Stride | Padding | Input Channel | Output Channel | Output Size | Receptive Field |
    | ------------------- | ----- | ---------- | --------------- | ------- | ------------- | -------------- | ----------- | --------------- |
    | Input Block         | Conv1 | 28         | 3 x 1           | 0       | 1             | 8              | 26          | 3               |
    | Convolution Block 1 | Conv2 | 26         | 3 x 1           | 0       | 8             | 16             | 24          | 5               |
    | Transition Block 1  | Pool1 | 24         | 2 x 2           | 0       | 16            | 16             | 12          | 6               |
    | Transition Block 1  | Conv3 | 12         | 1 x 1           | 0       | 16            | 8              | 12          | 6               |
    | Convolution Block 2 | Conv4 | 12         | 3 x 1           | 0       | 8             | 12             | 10          | 10              |
    | Convolution Block 2 | conv5 | 10         | 3 x 1           | 0       | 12            | 12             | 8           | 14              |
    | Convolution Block 2 | Conv6 | 8          | 3 x 1           | 0       | 12            | 16             | 6           | 18              |
    | Convolution Block 2 | Conv7 | 6          | 3 x 1           | 1       | 16            | 16             | 6           | 22              |
    | Output Block        | GAP   | 6          | 3 x 1           | 0       | 16            | 16             | 1           | 28              |
    | Output Block        | Conv8 | 1          | 1 x 1           | 0       | 16            | 10             | 1           | 28              |

- **Observation**

  - Model has got maximum training accuracy as 98.63%. It still can be trained more.

  - We will add batch normalization in the next iteration hoping for the training to be faster and accuracy to be increased.

## MODEL - 2 : INTERIM [[model_2_interim.ipynb]](./model_2_interim.ipynb)

### TARGET

- Here we introduced Batch Normalisation in all the layers except Transition layer and last Output layer.

- In the next interation, we introduced dropout to make the training harder.

- As we are not able to achive desired result, we introduced image augmentation.

### RESULT

- After adding **Batch Normalisation**

  - Parameters : **7864**

  - Best Train Accuracy : **99.51%**

  - Best Test Accuracy : **99.31%**

- After adding **Dropout**

  - Parameters : **7864**

  - Best Train Accuracy : **99.42%**

  - Best Test Accuracy : **99.26%**

- After adding **Image Augmentation**

  - Parameters : **7864**

  - Best Train Accuracy : **99.24%**

  - Best Test Accuracy : **99.35%**

### Analysis

- Batch Normalisation

  - After introducing **batch normalisation**, the model is fitting very well.

  - It has got 99.51% as training accuracy and 99.31% of Test accuracy. Also gap between both accuracy is very narrow.

  - This is fairly well but still not achived the required target of 99.4%.

  - Since there is little over-fitting, we will introduce some regularisation so that model can generaize the leraning and test accuracy can be improved.

  - We will add little dropout to all the layers.

- Dropout

  - Even after adding **dropout** training is happening more robustly. Regularisation is going well. But the accuracy didn;t improve much.

  - Lets add augmentation techniques to make the training more robust.

- Image Augmentation

  - After adding **image augmentaion** in training dataset, Test accuracy increased a little more.

  - It achived test accuracy of 99.35%.

  - But one observation is the accuracy is fluctuating between values. Hence step scheduler can help.

## MODEL - 3 : FINAL [[model_3_final.ipynb]](./model_3_final.ipynb)

### TARGET

- Here we introduced StepLR learning rate scheduler.

- Experimented with optimiser as well.

- Able to get desired result **consistently** from 7th epoch onwards, all documented as below

### RESULT

- With **StepLR and SGD**

  - Parameters : **7864**

  - Best Train Accuracy : **99.30%**

  - Best Test Accuracy : **99.40%**

---

- After adding **StepLR and ADAM**

  - Parameters : **7864**

  - Best Train Accuracy : **99.55%**

  - Best Test Accuracy : **99.48%**

### Analysis

- After adding scheduler the model was able to achive its objective performance of 99.4% test accuracy under 15 epcoh under 800 parameters

- Here I am giving 2 results from 2 optimisers

  - **Leraning Rate scheduler with SGD**

  | Epoch | Training Accuracy | Test Accuracy | Diff  | Training Loss | Test Loss |
  | ----- | ----------------- | ------------- | ----- | ------------- | --------- |
  | 0     | 88.16%            | 97.17%        | -9.01 | 0.1259        | 0.1030    |
  | 1     | 97.83%            | 98.61%        | -0.78 | 0.0668        | 0.0510    |
  | 2     | 98.31%            | 98.51%        | -0.20 | 0.0186        | 0.0499    |
  | 3     | 98.45%            | 98.98%        | -0.53 | 0.0682        | 0.0353    |
  | 4     | 98.67%            | 98.97%        | -0.30 | 0.0135        | 0.0356    |
  | 5     | 98.81%            | 99.15%        | -0.34 | 0.0340        | 0.0280    |
  | 6     | 99.08%            | 99.31%        | -0.23 | 0.0057        | 0.0228    |
  | 7     | 99.17%            | 99.35%        | -0.18 | 0.0274        | 0.0219    |
  | 8     | 99.21%            | 99.29%        | -0.08 | 0.1171        | 0.0220    |
  | 9     | 99.25%            | 99.39%        | -0.14 | 0.0839        | 0.0214    |
  | 10    | 99.20%            | 99.34%        | -0.14 | 0.0466        | 0.0223    |
  | 11    | 99.23%            | 99.36%        | -0.13 | 0.0114        | 0.0212    |
  | 12    | 99.24%            | 99.37%        | -0.13 | 0.0081        | 0.0216    |
  | 13    | 99.25%            | 99.40%        | -0.15 | 0.0130        | 0.0211    |
  | 14    | 99.30%            | 99.40%        | -0.10 | 0.0250        | 0.0208    |

  ***

- **Learning Rate scheduler with ADAM optimiser**

  | Epoch | Training Accuracy | Test Accuracy | Diff  | Training Loss | Test Loss |
  | ----- | ----------------- | ------------- | ----- | ------------- | --------- |
  | 0     | 94.47%            | 97.62%        | -3.15 | 0.0283        | 0.0770    |
  | 1     | 98.11%            | 98.69%        | -0.58 | 0.0118        | 0.0396    |
  | 2     | 98.39%            | 98.63%        | -0.24 | 0.0808        | 0.0460    |
  | 3     | 98.63%            | 99.10%        | -0.47 | 0.0096        | 0.0250    |
  | 4     | 98.75%            | 98.63%        | 0.12  | 0.0096        | 0.0398    |
  | 5     | 98.84%            | 99.17%        | -0.33 | 0.0115        | 0.0267    |
  | 6     | 99.16%            | 99.41%        | -0.25 | 0.0030        | 0.0187    |
  | 7     | 99.36%            | 99.43%        | -0.07 | 0.0022        | 0.0181    |
  | 8     | 99.42%            | 99.48%        | -0.06 | 0.0168        | 0.0168    |
  | 9     | 99.44%            | 99.41%        | 0.03  | 0.0102        | 0.0187    |
  | 10    | 99.40%            | 99.46%        | -0.06 | 0.0030        | 0.0178    |
  | 11    | 99.46%            | 99.43%        | 0.03  | 0.0076        | 0.0173    |
  | 12    | 99.48%            | 99.43%        | 0.05  | 0.0025        | 0.0175    |
  | 13    | 99.51%            | 99.44%        | 0.07  | 0.0032        | 0.0172    |
  | 14    | 99.55%            | 99.45%        | 0.09  | 0.0024        | 0.0170    |
