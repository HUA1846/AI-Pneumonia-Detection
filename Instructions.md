## Evaluation

Your project will be reviewed by a Udacity reviewer against the [project rubric](https://review.udacity.com/#!/rubrics/2815/view). Be sure to review this rubric thoroughly and self-evaluate your project before submission. All criteria found in the rubric must be meeting specifications for you to pass.

## Submission Files

Following files would be needed for evaluation:

- EDA.ipynb notebook file with all questions answered and all code cells executed and ***displaying output.\***
- Build and train model.ipynb notebook file with all questions answered and all code cells executed and ***displaying output.\***
- A .h5 or .json file contains your final model architecture.
- A .h5 or .json file contains your final model weights.
- Inference.ipynb notebook file with all questions answered and all code cells executed and ***displaying output.\***
- FDA_submission.pdf file with a report that describes the algorithm and performance.

Please upload the FDA_submission.pdf to the workspace and submit your project through the workspace.

## Starting the project

For this project, you will work in the Jupyter GPU workspace provided for you. You can also find the notebooks containing the necessary starter code in the workspace.

You may also download all of the files for the project directly from [this repo](https://github.com/udacity/AIHCND_C2_Starter). This workspace contains:

- `EDA.ipynb`: This is the file you will be performing the EDA.
- `Build and train model.ipynb`: This is the file you will be building and training your model.
- `Inference.ipynb`: This is the file you will be performing clinical workflow integration.
- `.dcm` files: They are the test files to test the clinical workflow integration.
- `sample_labels.csv`: This is the file that should be used to assess images in the pixel-level.
- `FDA_Submission_Template.md`: This is the template for you to create the FDA submission. Please copy the template into your choice of editor. Finish the documentation, save it as a .pdf file, and upload.

**Note:** The NIH data for EDA and training is mounted in the Udacity Jupyter GPU workspace provided to you along with the code to load the data. Alternatively, you can download the data from the [kaggle website](https://www.kaggle.com/nih-chest-xrays/data) and run it locally. You are STRONGLY recommended to complete the project using the Udacity workspace since the data is huge, and you will need GPU to accelerate the training process.

Detailed instruction for this project is provided in this [**README**](https://github.com/udacity/AIHCND_C2_Starter/blob/master/README.md) file.



## From Discussion Forum

##### Pixel level intensity

You have to perform following steps to draw conclusions from the EDA:-

1. Firstly you have to make comparison of pixel intensity graph between Pneumonia and Non Pneumonia Images and see whether if they have a similar distribution and report it.
2. To find the similarities between the distribution you have to look for mean and mode of the image and ask yourself are they close to each other And also the general shape of the graph do the intensity level graph for both the cases look same (can you easily distinguish between them or not just by taking a rough look at them), take these factors into account and report whether the 2 cases are identical or not.
3. Secondly, You have to make comparison of pixel level intensity of Pneumonia with all the 14 disease labels present in the dataset (use at least 3 images per disease) and figure out if there any disease that have similar distribution as that of Pneumonia and report them.
4. As a hint I can tell you that there are at least 2 such diseases that you need to find and report.

```python
paths_img = {os.path.basename(c): c for c in glob(os.path.join('/data','images*', '*', '*.png'))}
sample_df['path_img'] = sample_df['Image Index'].map(paths_img.get)

hist = sample_df[sample_df["Finding Labels"].str.match('Pneumonia')]
data = []
for p in range(9):
    data.append(("pneumonia", hist.iloc[p]["path_img"]))
    
plt.figure(figsize=(15, 15))
columns = 3
for i in range(len(data) ):
    plt.subplot(len(data) / columns + 1, columns, i + 1).set_title('pneumonia')
    var = plt.imread(data[i][1])
    plt.hist(((var - np.mean(var))/np.std(var)).ravel(), bins=250)
```

