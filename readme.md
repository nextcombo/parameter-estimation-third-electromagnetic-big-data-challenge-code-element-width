# [Parameter Estimation] Third Electromagnetic Big Data Challenge: Code Element Width Regression Prediction Network Technology [with Python code]

I. Key Technologies: Binary Waveform Width Regression Prediction Network Design (I) Data Preprocessing: IQ Phase Correction 1
Core principle: By constructing a rotation matrix, compensate for the fixed phase offset of the signal to ensure the accuracy of subsequent feature extraction.
2  
Implementation steps:
•  
Extract the first N samples of the signal, denoted as s[n] = I[n] + jQ[n] (where I is the phase component and Q is the quadrature component).
•  
Calculate the mean phase difference between each sampling point and the first sampling point, to achieve global phase difference estimation.
•  
Based on the estimated phase difference, construct a rotation matrix to compensate for phase offset (compensating angle is -Δφ).
1  
Advantages and Disadvantages:
•  
Advantage: The algorithm is simple, easy to implement in engineering, and can effectively correct fixed phase deviations.
•  
Drawback: It is only applicable to fixed phase offset scenarios. When the signal-to-noise ratio is too low, the error in phase difference estimation will significantly increase.
(II) Network Design: Multi-feature Integration + Transformer as the Main Body
Input (IQ signal) → Standardization → Multi-feature extraction (time domain + frequency domain + statistical) → Feature fusion (including SE module) → Relative position perception Transformer Encoder → Regression head → Output (code width)
Feature type
Method of extraction
Key details
Output dimension (example)
Time-domain features
Multi-scale ResNet
- Utilizes 4 residual blocks for information compression and abstraction; - Sets up 3 scales (scale=1,2,4), covering different receptive fields; - Incorporates an SE module to enhance the feature representation of key channels.
(batchsize, 512, 32)  
Frequency domain features
FFT+Conv1D  
- Perform FFT transformation on the IQ signal, extracting amplitude spectrum (ABS) and phase spectrum (Angle); - Compress features through 1D convolution (Conv1D) and max pooling (MaxPool1D).
(batchsize, 256, 32)  
Statistical features
Statistical Calculation + Conv1D
- Calculate six types of statistical measures: mean (Mean), standard deviation (Std), maximum (Max), minimum (Min), kurtosis (Kurtosis), and skewness; - Integrate 1D convolution into high-dimensional statistical features.
(batchsize, 128, 32)  
3. Core Module Design
•  
Residual Block (ResBlock):
•  
Two structures: When the stride (stride) is not equal to 1 or the number of input channels (in_c) is not equal to the number of output channels (out_c), the short-circuit loop contains Conv1D (matching dimensions); otherwise, the short-circuit loop directly connects.
•  
Activation Function: Gelu is used to enhance the nonlinear representation capability.
•  
Regularization: BatchNorm1D and Dropout included to avoid overfitting.
•  
SE module (squeeze-and-excitation):
•  
Process: AdaptiveAvgPool1D → Linear layer (Linear) → ReLU → Linear layer → Sigmoid → Feature weight assignment;
•  
Function: Learning inter-channel dependencies, enhancing effective features and suppressing redundant ones, thereby improving the feature extraction performance of ResNet.
•  
Relative Positional Perception Transformer Encoder:
•  
Structure: Only contains Encoder (3 layers), using relative position encoding (Relative Pos. Enc.) to solve the problem of signal timing dependency;
•  
Component: Contains multi-head attention and feed forward network, outputs high-dimensional logical features.
•  
Feature fusion: receive time domain + frequency domain + statistical features (concatenated dimension is (batchsize, 32, 512)), and stabilize training through Add & Norm.
•  
Regressor:
•  
Process: Linear → Mean → BatchNorm1D → Relu → Dropout → Linear → Squeeze;
•  
Output: Single value (symbol width), dimension is (batchsize, 1).
II. Experimental Assessment (I) Training Strategies and Loss Curve
Training parameters: After preprocessing the official dataset, the batch size (batchsize) is 128.
2  
Three-stage training:
•  
Stage 1: Using the RAdam optimizer, train for 50 rounds to achieve rapid loss reduction.
•  
Stage 2: Switch to SGD Optimizer, continue training, Loss slowly decreases.
•  
Stage 3: Fine-tuning with AdamW optimizer, the loss fluctuates around a stable value, and the model converges.
(II) Model Performance Metrics 1
Core Result:
•  
Best SW score: around 92 points;
•  
Relative error: The majority of prediction results have an error within 20%.
•  
Linearity of fit: The predicted values are generally linearly related to the actual values, with an R²=0.97, indicating the model's effectiveness.
1  
Errors and Fit Visualization:
•  
Error Frequency Distribution: Absolute errors are concentrated within the [-0.2, 0.2] interval, with a uniform distribution;
•
Predicted vs Actual Values: Data points closely align with the "ideal prediction line," and the SW score is positively correlated with actual values.
Model configuration for ablation experiments (verifying the role of each module)
SW Score
R²
Conclusion (comparing the final model (e))
(a) ResNet + Linear
75.42
0.899
Only using ResNet + linear layer results in poor performance due to lack of Transformer and regression head
(b) ResNet + Transformer + Linear
85.28
0.933
Adding Transformer improves temporal modeling capability by ~10 points after incorporating it
(c) ResNet + Transformer + Regressor
90.18
0.966
Substituting Linear with a regression head increases regression accuracy, with R² approaching 0.97
(d) Without IQ phase correction (c)
85.17
0.921
Removing IQ phase correction leads to a performance drop of ~5 points due to phase bias
(e) Final Model (fully configured)
92.02
0.970
Multi-feature fusion + Transformer + regression head + IQ correction results in optimal performance
Ablation conclusion: IQ phase correction, Transformer temporal modeling, and dedicated regression heads are key modules for improving model performance, and multi-module collaboration can significantly enhance prediction accuracy.
Core summary 1
Technical highlights: By utilizing IQ phase correction to address signal phase deviations, this system integrates multi-scale ResNet (time domain), FFT (frequency domain), and statistical measures (statistical features) to achieve full-dimensional feature extraction. It employs Transformer to capture temporal dependencies, ultimately outputting precise code element widths through a regression head.
2  
Performance Advantage: SW score reaches 92, R²=0.97, error controllable. It performs excellently in the task of predicting the width of electromagnetic signal code bits and can be used as a reference solution for similar problems.
3  
Engineering Value: The model structure is clear, the modules are reusable, the training strategy is stable, suitable for engineering implementation, and provides technical support for electromagnetic big data processing.
End

## Images

![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](01.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](02.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](03.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](04.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](05.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](06.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](07.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](08.jpg)
![(Parameter Estimation)The Third Electromagnetic Big Data Challenge: Coded Width Regression Prediction Network Technology [with Python code]](09.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
