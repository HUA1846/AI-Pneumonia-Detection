# FDA  Submission

**Your Name:** Peihua Lee 

**Name of your Device:** ChestX-ray Pneumonia Detector 

## Algorithm Description 

### 1. General Information

**Intended Use Statement:** 

This algorithm is intended for assisting radiologists in the detection of pneumonia on chest x-rays.

**Indications for Use: **This algorithm is indicated for use on people of both gender from the age of 1 to 95 who have been administered a diagnostic chest x-ray on a digital X-ray machine to assist radiologists to detect pneumonia.

**Device Limitations:** 

- Algorithm limitations:

  This device was not trained on people over age of 90 and under age of 2. The algorithm may not perform well in the presence of  the following comorbid thoracic pathologies due to similar image pixel intensity distributions:

  1. cardiomegaly
  2. consolidation
  3. effusion
  4. mass
  5. pleural thickening

  | Pneumonia                                                    | Pneumonia                                                    |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/pneumonia-2.PNG?raw=true" alt="pneumonia-2.PNG" style="zoom: 50%;" /> | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/pneumonia-1.PNG?raw=true" alt="pneumonia-1.PNG" style="zoom:50%;" /> |
  
  ##### Pathologies with similar intensity distributions as pneumonia:
  
  | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/cardiomegaly.PNG?raw=true" alt="cardiomegaly.PNG" style="zoom:50%;" /> | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/consolidation.PNG?raw=true" alt="consolidation.PNG" style="zoom:50%;" /> | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/effusion.PNG?raw=true" alt="effusion.PNG" style="zoom:50%;" /> |
  | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/mass.PNG?raw=true" alt="mass.PNG" style="zoom:50%;" /> | <img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/pleural%20thickening.PNG?raw=true" alt="pleural thickening.PNG" style="zoom:50%;" /> |                                                              |



- Hardware limitations:  

  The algorithm's performance was measured on Intel(R) Core(TM) i5-7200U CPU @ 2.50GHz 2.70GHz.

  Average time to process each image is 0.85 seconds.

**Clinical Impact of Performance:**

This algorithm is aimed to aid radiologist to diagnose pneumonia. Given it's low false negative rate, radiologists can be confident with the device's prediction on the negative cases. Thus, radiologists can focus on reviewing images of those positive cases identified by the device and make those who require medical attention to receive treatment faster.

### 2. Algorithm Design and Function

<img src="https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/flow%20chart.png?raw=true" alt="flow chart.png" style="zoom:67%;" />

**DICOM Checking Steps:**

- Check modality type: modality has to be DX (Digital X-ray), if not, image will not be processed. Show error message.
- Check patient position: position has to be PA (posterior-anterior) or AP (anterior-posterior), if not, image will not be processed. Show error message.
- Check body part examined: body part has to be chest, if not, image will not be processed. Show error message.

**Preprocessing Steps:**

- Convert RGB scale to Grayscale as needed
- Resize image to 244 x 244 for CNN
- Normalize intensity to from the original 0-255 range to 0-1.

**CNN Architecture:**

This algorithm uses the pre-trained VGG16 CNN model with the first 17 layers freezed for training. Additional two blocks of dropout layers and dense layers were added for training model fine-tuning. The model output a single probability value for binary classification.

CNN architecture graph:

![model summary.PNG](https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/model%20summary.PNG?raw=true)


### 3. Algorithm Training

**Parameters:**
* Types of augmentation used during training

  | Types of augmentation | Parameters |
  | :-------------------: | :--------: |
  |        rescale        |   1/ 255   |
  |    horizontal_flip    |    True    |
  |     vertical_flip     |   False    |
  |  height_shift_range   |    0.1     |
  |   width_shift_range   |    0.1     |
  |    rotation_range     |     20     |
  |      shear_range      |    0.1     |
  |      zoom_range       |    0.1     |

* Batch size: 32

* Optimizer learning rate: 6e-6

* Layers of pre-existing architecture that were frozen:  all except last convolution and pooling block

* Layers of pre-existing architecture that were fine-tuned:  The last two layers of VGG16: block5_conv3, block5_pool

* Layers added to pre-existing architecture

Algorithm training performance visualization:

Applied early stopping with patience of 10 epochs of no improvement on validation loss. High validation loss may be due to higher learning rate. Decreasing learning rate may be able to decease validation loss. While train accuracy increased smoothly during model training, validation accuracy increased over training, but also showed some setbacks. This may indicate overfitting of the train data.  May decrease overfitting by applying early stopping (applied in this algorithm), increasing dropout rate, and decreasing CNN layers or numbers of units per layer. Increasing training data is also possible to decrease overfitting if more data is available in the future.

![training loss and accuracy.png](https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/training%20loss%20and%20accuracy.png?raw=true)



![roc_curve.png](https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/roc_curve.png?raw=true)

**Final Threshold and Explanation:**

![F1.png](https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/F1.png?raw=true)

The maximum F1 score of this algorithm is 0.46, and it is achieved at threshold value of 0.33856. The model's F1 score exceeds the average radiologist performance (0.387) on the pneumonia detection task according to this study<sup>1</sup>. 

The maximum precision score of this algorithm is 0.305, indicating high false positive rate. This could be due to the device limitations on differentiating certain thoracic pathologies from pneumonia. Thus, when the algorithm predicts a positive result, the probability of the result being truly positive is low.

The maximum recall value of this algorithm is 0.97, indicating low false negative rate. Thus, if the algorithm predicts an x-ray image to be negative for pneumonia, the probability of it being truly negative is high.

**Description of Training Dataset:** 

Training dataset consists of 2290 chest x-ray images with 50% being positive and 50% being negative for pneumonia.

Age: 2-90

Sex: Male and Female

Disease prevalence: training dataset includes 50% of pneumonia cases and 50% non-pneumonia

**Description of Validation Dataset:** 

Validation dataset consists of 1430 chest x-ray images with 20% of positive cases, and 80% of positive cases, which reflects the real world pneumonia occurrence rate showing in chest x-rays. 

Age: 2-90

Sex: Male and Female

Disease prevalence: validation dataset includes 20% of pneumonia cases and 80% non-pneumonia

Sample Chest X-rays: 

|                          Pneumonia                           |                         No Pneumonia                         |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![pneumonia_xray.png](https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/pneumonia_xray.png?raw=true) | ![no_pneumonia_xray.png](https://github.com/HUA1846/AI-Pneumonia-Detection/blob/main/images/no_pneumonia_xray.png?raw=true) |




### 5. Ground Truth

Ground truth of the NIH dataset:

The finding labels were extracted using natural language processing (NLP), so there could be some erroneous labels but the NLP labeling accuracy is estimated to be >90% . NLP-derived labels are sub-optimal and could impact algorithm performance.

In this device, we treat the finding labels as ground truth for the purpose of classification. There are 14 common thorax disease categories in this dataset. Images can be labeled as "No findings" if none of the diseases were found.

1. Atelectasis

2. Cardiomegaly
3. Effusion
4. Infiltration
5. Mass
6. Nodule
7. Pneumonia
8. Pneumothorax
9. Consolidation
10. Edema
11. Emphysema
12. Fibrosis
13. Pleural_Thickening
14. Hernia

### 6. FDA Validation Plan

**Patient Population Description for FDA Validation Dataset:**

The following population's chest x-ray subset is to be used for the FDA validation dataset:

- Gender: male and female
- Age: 2-90
- Without these five thoracic pathologies: cardiomegaly, consolidation, effusion, mass, pleural thickening

**Ground Truth Acquisition Methodology:**

Silver standard: NLP extraction

**Algorithm Performance Standard:**

Clinical performance of this algorithm can be measured by calculating the F1 score from the precision score and recall score.  This algorithm's maximum F1 score is which exceeds the average radiologists performance F1 score stated in this past research<sup>1</sup>.  But it is important to know that neither this algorithm nor the radiologists had access to patient history. Lack of patient history can lead to inaccurate diagnosis. This past research conducted multi-label disease classification on chest x-rays, and generated bounding boxes for disease localization<sup>2</sup>. Although the research was not a pure binary classification algorithm, it is worth knowing that it's maximum pneumonia detection accuracy is 0.75. This algorithm's validation binary accuracy reached 0.74219, which was very close to the research.

### References

1. Rajpurkar, R. et al. 2017 CheXNet: Radiologist-Level Pneumonia Detection on Chest X-Rays with Deep Learning

2. Wang X. et al. 2017 ChestX-ray8: Hospital-scale Chest X-ray Database and Benchmarks on Weakly-Supervised Classification and Localization of Common Thorax Diseases

