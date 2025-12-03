# Samsung's Calibration for Post-Training  Quantization

## Calibration
Samsung quantization tool uses the MLPerf calibration datasets to extract statistics from the reference floating point neural network model for post-training quantization. The statistics collected using the calibration datasets include min, max, average, and standard deviation of the activations.
Either the full set of calibration images or a sampled subset is used. 
Specifically, resize and crop operations are applied to these datasets. For Imagenet classification tasks, input images for MobileNetEdgeV2 are resized to 224x224, while images for MobileNetV4 are resized to 384x384. COCO dataset is resized to 320x320. Lastly, ADE20k dataset images are resized such that the shortest size of each image is 512, then a center crop of 512x512 is used.

### Calibration Datasets
The calibration sets used in this submission are as agreed upon by the MLPerf Mobile Working Group for the classification, object detection, semantic segmentation, and super resolution benchmarks. For language understanding benchmark, the quantization-aware training (QAT) TensorFlow Lite model of MobileBert is directly utilized and converted into our custom format model without employing calibration data. The list of used calibration images are shown below:

- ImageNet: https://github.com/mlcommons/mobile/blob/master/calibration/ImageNet/cal_image_list_option_1.txt
- COCO: https://github.com/mlcommons/mobile/blob/master/calibration/COCO/coco_cal_images_list.txt
- ADE20K: https://github.com/mlcommons/mobile/blob/master/calibration/ADE20K/ADE20k_calibration.txt
- OpenSR: 
    * Out_hr https://github.com/mlcommons/mobile_models/releases/download/v3.0-snusr/snusr_hr_nchw.zip 
    * In_lr https://github.com/mlcommons/mobile_models/blob/main/v3_1/datasets/snusr_lr_png.zip

## Quantization
Samsung's quantization tool can perform per-channel or per-layer quantization, and using either symmetric or asymmetric quantization. The input to this quantization tool is the FP32 TFLite model. The output of this quantization tool is a Fixed-Point (FXP) proprietary model.

### Weights
The quantization range is determined from the statistics of the pre-trained weights. Given the quantization granularity (#bits), the scaling factor and zero point are computed which in turn are used to quantize the weights.

### Activations
The quantization parameters are first obtained through profiling the neural network using calibration set. Profiling is the process of acquiring statistics of the activations by feed-forwarding a set of calibration data. 
Examples of the statistics collected are the min, max, mean and standard deviation values of the activations at a per-channel level for each layer (e.g., for each Convolutional layer). These statistics are used to calculate quantization parameters for each channel.
For quantization, it is assumed that the activations follow a specific probabilistic distribution. Examples of the distributions considered are as follows:

-	Uniform distribution
-	Laplace distribution
-	Normal distribution
-	Logistic distribution
-	Hyperbolic secant distribution
-	Cauchy distribution

For the models quantized, the probabilistic distribution that best fits the distribution of activations in the entire neural network is found. The probabilistic distribution is selected to minimize the quantization loss compared to the floating point model.
For classification models, the quantization loss can be measured based on the differences in predictions obtained from the floating point and the quantized models. For other tasks, the quantization loss can be measured by a distance norm between feature maps of the quantized models and the floating point models.
Once the distribution is selected, the optimal quantization parameters are computed.

For Stable Diffusion benchmark, a mixed‑precision quantization scheme is employed wherein the model parameters (weights) are represented using 8‑bit precision, while the intermediate activations are encoded with 8‑bit or 16‑bit precision. The selection of the specific bit‑width for each layer is driven by a per‑layer sensitivity analysis of the quantization loss as defined above.

### Bias Compensation
The quantization parameters are refined to compensate any biases introduced from quantizing the network weights and activations. This is achieved by computing the expected quantization errors of both weights and activations, per layer, by comparing performances of the quantized model and the reference floating point network on the reference calibration set. The expected errors are then added to the layer bias parameters to mitigate the quantization bias.

Exact conversion scripts to reproduce the model quantizations of MLPerf models are included with ENN SDK, upon request.
