# Meta-Learning: Build classifier and classify data

## About this Tutorial <a id="about-this-tutorial"></a>

Maana Meta-Learning service is an automated machine learning service that builds machine learning pipelines for classification tasks under your guidance. Given a kind, you can specify feature fields and a label field, a decision space containing candidate featurizers, pre-processors and models.

Meta-Learning service explores the decision space, builds up and tests pipelines, performs statistical analysis on their performances, and deliver insights to you on the overall performances of each machine learning components, and the an overall best pipeline. The goal of Meta-Learning service is to facilitate data-scientists to quickly profile on data set, test their hypothesis and rapidly build new solutions.

## Meta-Learning Service Inside Platform <a id="metalearning-service-inside-platform"></a>

### Train the Classifier <a id="train-the-classifier"></a>

1. Upload a CSV file, in this case use [small\_census](https://github.com/maana-io/q-tutorials/blob/master/maana-metalearning/small_census.csv).

2. Load the data into the platform.

3. Bring the Kind for the small\_census.csv file into the workspace by clicking the link on the bottom of small\_census.csv - the Kind will be called "SmallCensusCSV".

4. As soon as the CSV file is uploaded, the field classifier is kicked off and classifications for each of the columns of the tabular data are produced.

![Figure 1: View after uploading CSV and Clicking on kind link](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKz7QFC0zDuR-A1cAs%2Fimage.png?alt=media&token=ddfe2cb6-6d50-4fe6-b691-d884fa7d201c)

5. This kind contains instances that describe individual's age, work class, education, martial status, etc and its salary range. You'll want to build a classifier that predicate an individual's salary based on some of these features.

6. To trigger Meta-Learning service, open the GraphQL interface of Meta-Learning by Click "Maana Meta-learning" in "Inventory" panel:

![Figure 2: Open MetaLearning GraphQL interface from Inventory](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzCC0uviCpXYnfenA%2Fimage.png?alt=media&token=fac7c4ea-f14e-4dda-86a0-83211f564898)

7. From the GraphiQL interface, use TrainClassifierKind mutation to train a classifier for SmallCensusCSV:

![Figure 3: Train Classifier from kind SmallCensusCSV](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzHbAUWRe56519g5t%2Fimage.png?alt=media&token=a84b26da-dbba-4080-bef0-fd97bd740499)

```text
mutation smallcensus {
  trainClassifierKind(input:{
    accuracy:0.5,
    kindId:"KindID",
    modelName:"SmallCensusModel", # Must be unique, not previously used
    labelField: "salary",
    excludeFields: ["id"],
    featureFields: ["age", "workclass", "education", "maritalStatus", "race", "sex"],
    featureTypes: ["integer", "categorical", "categorical", "categorical", "categorical", "categorical"],
    candidateModels: ["random_forest_classifier", "logistic_classifier"],
    candidatePreprocessors: ["noop", "pca"],
    folds:2,
    modelSearchEpisodes:2,
    modelProfilingEpisodes:4
  })
}
```

8. In the above mutation, the kindID field is filled in with the id of kind "SmallCensusCSV". You give a model name, and identify label field, feature fields, candidate models and candidate pre-processors. You also specify to perform 2-fold cross validation for model selection, and perform 4 episodes of hyper-parameter sampling and 2 episodes of hyper-parameter search.

9. To visualize the results, search for kind "Dataset", and drag it to the workspace, then click the link at finalModel. The kind MachineLearningModel will show on the canvas.

![Figure 4: Trained machine learning models](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzMsrp7a-MmD8TeGS%2Fimage.png?alt=media&token=b05888fe-5910-4e07-8023-adbd0d3dde22)

10. From the dataview panel, it shows each pipeline that has been built and tested, including its model, features, pre-processors, time to learn, accuracy, and the stage they are tested. Look for the pipeline where "saved" is "True": this is the final pipeline that is built as the best.

![Figure 5: Built model](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzQfSG07nv-pdT0Oc%2Fimage.png?alt=media&token=fad5b504-d202-4bb5-9335-eaf922db3ffd)

11. Following the link of features, pre-processor and labels you can see the kind that stores the detailed information:

![Figure 6: Detailed information in Kinds](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzUZFDwAckFDsbKzz%2Fimage.png?alt=media&token=5fee8600-51b1-466f-ba47-d0fb130a58e4)

![Figure 7: Preprocessor information in Kind](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzZPgY_KImzXlEIMB%2Fimage.png?alt=media&token=839eb577-1830-4b1f-ab88-87e59e8f475a)

![Figure 8: Featurizer information in Kind](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzcBMGxS90Me7OLNQ%2Fimage.png?alt=media&token=37a381b7-c90f-451c-864f-0c476241eaa3)

### Classify Instance <a id="classify-instance"></a>

1. Using mutation ClassifyInstance, you can use a trained model to classify instance.

![Figure 9: Classify instance](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzhVrXPKFgYn3po4j%2Fimage.png?alt=media&token=e6b01e69-7ddc-4b8c-ba87-debd2c07a99a)

```text
mutation classify {
  classifyInstance(input: {
    trainedModelId: "SmallCensusModel",
    data:["{\r\n  \"age\": \"39\",\r\n  \"workclass\":\" State-gov\",\r\n  \"fnlwgt\":\" 77516\",\r\n  \"education\":\" Bachelors\",\r\n\t\"educationnum\":\" 13\",\r\n  \"maritalstatus\":\" Never-married\",\r\n  \"occupation\":\" Adm-clerical\",\r\n  \"relationship\":\" Not-in-family\",\r\n  \"race\":\" White\",\r\n  \"sex\":\" Male\",\r\n  \"capitalgain\":\" 2174\",\r\n  \"capitalloss\":\" 0\",\r\n  \"hoursperweek\":\" 40\",\r\n  \"nativecountry\":\" United-States\"\r\n}"]
  })
}
```

### Classify Kind <a id="classify-kind"></a>

Using mutation ClassifyKind, you can use a trained model to classify a kind and make prediction to be a new column. 1. Drag CSV file [small\_census\_new](https://github.com/maana-io/q-tutorials/blob/master/maana-metalearning/small_census_new.csv) to your workspace. This CSV file contains all feature fields of smallcensus, but there is no field for salary.

2. After the CSV file is loaded to workspace, click the link to the kind small\_census\_newcsv and preview the data:

![Figure 10: Create a Kind for Classification](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzmK5E9n26IVV1Kxe%2Fimage.png?alt=media&token=78a8287f-a6ec-431f-a731-c48afae2d1d8)

3. Now use the following mutation:

![Figure 11: Classify Kind](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzqBMN4aCt3SrSzsp%2Fimage.png?alt=media&token=dc890ac3-a838-40fd-a9fa-9bb669773505)

```text
mutation classify
{
  classifyKind(input:
  {
    trainedModelId:"SmallCensusModel",
    fromKindId:"<insert kind id>",
    fromInstanceIdentifierFieldName:"salary"
  }
  )
}
```

4. Go back to the workspace, a new field "salary" is added to small\_census\_newcsv kind with the classification results:

![Figure 12: Result of classifying kind](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LWSKjuJIsK0lFXCaEtL%2F-LXKyuuNj95-UqghaQwM%2F-LXKzu5QfztJ6-WCmYUq%2Fimage.png?alt=media&token=eab155c2-4690-4d16-a6e4-8b6800af76ac)

