## **Post-Training Quantization**
SNPE uses following post-training quantization procedures. For models, where ml-perf reference INT8 models are used, only activation quantization is applicable (weights are used from the reference model).
In general, uniform, asymmetric, per-tensor quantization is used for both weights and activations.

For Stable Diffusion, AI Model Efficiency Toolkit (AIMET) is used to create quantization simulation models (QuantSim) for the text encoder, U-Net, and VAE using a mixed-precision quantization scheme. A calibration process is used to create these QuantSim models where per-layer quantization encodings are determined using representative data samples. The QuantSim models simulate running the stable diffusion models on a quantized target. In addition, for the Text Encoder model, the AIMET Adaptive Rounding (AdaRound) technique is applied to get a boost in quantized accuracy. [1]

## Weights and Biases
Each weight tensor is quantized using the standard tensorflow quantization method described in the [SNPE
documentation](https://docs.qualcomm.com/bundle/publicresource/topics/80-63442-2/overview.html). CLE is then applied which scales convolution weights over sets of convolution layers to equalize the weight ranges. During quantization biases are adjusted to correct the error bias due to weight quantization. Both techniques are outlined in the “Cross-layer equalization” and “Bias Correction” sections outlined in “Data-Free Quantization Through Weight Equalization and Bias Correction” [2].

For Stable Diffusion, each weight tensor is quantized using the method described here: https://docs.qualcomm.com/bundle/publicresource/topics/80-64748-1/model_updates.html

## Activations
For each activation tensor, the dynamic range is determined based on min and max values obtained when running inference over a set of representative input dataset (e.g., calibration images). This dynamic range sets the clipping boundaries for the uniform quantizer. In addition, activation quantization optimizations are applied when layer activation encodings can be propagated between math-invariant layers so as to reduce the incidences of requantization within the graph.

For Stable Diffusion, the activation bitwidth is set to INT16.


### References
[1] https://docs.qualcomm.com/bundle/publicresource/topics/80-64748-1/model_updates.html

[2] M. Nagel, M. Baalen, T. Blankevoort, M. Welling, Data-Free Quantization Through Weight Equalization and Bias Correction, ICCV’19 (https://arxiv.org/pdf/1906.04721.pdf)
