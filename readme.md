1) high-level description of the project, including motivation, goals, etc.
2) EDA:  data source + visualizations + feature engineering
3) description of your model and results (possible tables & visualization)
4) summary / interpretations
5) [optional] future work
c) folder `src` with necessary source code
d) presentation slides
e) folder `images` with images that your md file includes

# Analyzing Fad Diet Tweets
Vinh Tran

### Link to Presentation
Keynote: https://drive.google.com/file/d/1eELvOFhvU7BpXIzDhOLDOIlTbJy5KrYq/view?usp=sharing
PDF: https://drive.google.com/file/d/1RrDwgiRb_zyAFHyjcw0eih1gRjDUZj0p/view?usp=sharing

# Table of Contents
- [Description](#Description)
- [Strategy and Process](#Strategy-and-Process)
- [Overview of the Data](#Overview-of-the-Data)
- [Exploratory Data Analysis](#Exploratory-Data-Analysis)
- [Data Pipeline](#Data-Pipeline)
- [Model Selection](#Model-Selection)
- [Deep Learning](#Deep-Learning)
- [Emotional Analysis](#Emotional-Analysis)
- [Wordclouds](#WordClouds)
- [Conclusion and Next Steps](#Conclusion-and-Next-Steps)

# Description

I have a background in nutrition. When I bring this up, one of the first questions I always get is, “What do you think about <insert fad diet>?" Limitations of traditional nutrition research include: a large investment in money and time, involvement of human subjects, and not being agile enough to keep up with rapidly-changing trends. Measuring sentiment on social media is a way to guide research which overcomes these limitation. From a public health perspective, investments can be made into diets that are shown to have an association with an outcome like obesity. From a marketing perspective, companies can glean insights on what consumers are interested in. The diet and weight loss industry in the US is estimated to be worth $72 billion dollars.

#### Goals and Questions:
(1) Perform topic modeling and measure sentiment of "fad" diet tweets.  
- When people tweet about fad diets, what are the general topics they tweeting about? Is the tweet sentiment positive, negative or neutral?
(2) Determine if tweet sentiment is associated with city-level obesity rates.
- Given sufficient text, predict the personality type of the individual.


# Exploratory Data Analysis


First let's take a look at how much each personality type was represented in the data set in comparison to the population.

![](img/samplebarvpop.png)

The results were interesting, the least common personality types seemed to be most represnted in the dataset. In order to compare apples to apples, let's convert our sample count to a percentage and plot them side by side.

![](img/samplevpop.png)

For further EDA please look at the summary [here](ExploratoryDataAnalysis.md)

<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>


# Data Pipeline

<!-- #region -->
Let's create a data pipeline, it will aim to do the following:
- Standardize the text to ASCII
- Remove weblinks
- Tokenize the words
- Use a stemmer on the words
- Remove HTML decoding
- Remove punctuation
- Remove stopwords

The code to do this can be found [here](src/personality.py)

We make a pickle file that creates a list of words as seen below:
- These have been standardized, tokenized, stemmed and punctuations/stopwords have been removed

|index|type|posts|
|:---:|:---:|:---:|
|0|INFJ|[life-chang, experi, life, may, perc, experi, ...|
|1|ENTP|['m, find, lack, post, alarm, sex, bore, 's, p...|
|2|INTP|[cours, say, know, 's, bless, curs, absolut, p...|
|3|INTJ|[dear, intp, enjoy, convers, day, esoter, gab,...|
|4|ENTJ|[you, re, fire, 's, anoth, silli, misconcept, ...|

We also make a pickle file of the strings standardized, and stemmed as seen below:

|index|type|posts|
|:---:|:---:|:---:|
|0|INFJ|what has been the most life-chang experi in yo...|
|1|ENTP|i 'm find the lack of me in these post veri al...|
|2|INTP|of cours to which i say i know that 's my bles...|
|3|INTJ|dear intp i enjoy our convers the other day es...|
|4|ENTJ|you re fire that 's anoth silli misconcept tha...|

Next we create another pickle file where the full process has been applied:

|index|type|posts|
|:---:|:---:|:---:|
|0|INFJ|lifechang experi life may perc experi immers h...|
|1|ENTP|im find lack post alarm sex bore posit often e...|
|2|INTP|cours say know that bless curs absolut posit b...|
|3|INTJ|dear intp enjoy convers day esoter gab natur u...|
|4|ENTJ|your fire that anoth silli misconcept approach...|

Finally, we use the [Emotions Lexicon](http://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm), which was created by the National Research Council Canada, to derive emotions from the text and store that in a pickle file. The code for this can be found [here](src/emotions.py):

|emotion|anger|anticipation|disgust|fear|joy|negative|positive|sadness|surprise|trust|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|0|3|22|2|3|11|14|22|4|6|13|
|1|14|23|8|15|21|24|37|12|17|18|
|2|7|26|5|12|26|17|42|9|13|31|
|3|6|14|4|7|6|14|30|7|2|20|
|4|17|30|15|13|23|29|43|14|15|24|


<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>
<!-- #endregion -->

# Model Selection


Here we go through different machine learning algorithms in order to find a model that can predict the personalities. Random would be 1/16 or 0.0625. That is really low, so for our model let's aim to achiece results higher than 50%. The code for this can be found [here](NLP_Models.ipynb)

We will use the following models:
- [Random Forest](#Random-Forest-Classifier)
- [Gradient Boosting Classifier](#Gradient-Boosting-Classifier)
- [Naive Bayes](#Naive-Bayes)
- [Stochastic Gradient Descent](#Stochastic-Gradient-Descent)
- [Logistic Regression](#Logistic-Regression)


## Random Forest Classifier

    RandomForestClassifier(bootstrap=True, class_weight=None, criterion='gini',
            max_depth=5, max_features='auto', max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, n_estimators=1000, n_jobs=-1,
            oob_score=True, random_state=29, verbose=0, warm_start=False)

    oob_score_ = 0.3614985590778098


## Gradient Boosting Classifier

    GradientBoostingClassifier(criterion='friedman_mse', init=None,
              learning_rate=0.01, loss='deviance', max_depth=5,
              max_features=None, max_leaf_nodes=None,
              min_impurity_decrease=0.0, min_impurity_split=None,
              min_samples_leaf=1, min_samples_split=2,
              min_weight_fraction_leaf=0.0, n_estimators=100,
              n_iter_no_change=None, presort='auto', random_state=29,
              subsample=1.0, tol=0.0001, validation_fraction=0.1,
              verbose=0, warm_start=False)

    Accuracy_score = 0.6273530541682674

#### This is pretty good, we change our max_depth to 3 and try again.

    GradientBoostingClassifier(criterion='friedman_mse', init=None,
              learning_rate=0.01, loss='deviance', max_depth=3,
              max_features=None, max_leaf_nodes=None,
              min_impurity_decrease=0.0, min_impurity_split=None,
              min_samples_leaf=1, min_samples_split=2,
              min_weight_fraction_leaf=0.0, n_estimators=100,
              n_iter_no_change=None, presort='auto', random_state=29,
              subsample=1.0, tol=0.0001, validation_fraction=0.1,
              verbose=0, warm_start=False)

    Accuracy_score = 0.650787552823665

#### Here is a summary of the results:

        precision    recall  f1-score   support

            ENFJ       0.52      0.28      0.37        53
            ENFP       0.64      0.56      0.60       195
            ENTJ       0.77      0.38      0.50        80
            ENTP       0.71      0.53      0.61       221
            ESFJ       0.50      0.38      0.43         8
            ESFP       0.60      0.30      0.40        10
            ESTJ       0.50      0.27      0.35        11
            ESTP       0.67      0.38      0.48        32
            INFJ       0.65      0.72      0.69       424
            INFP       0.61      0.84      0.71       571
            INTJ       0.65      0.59      0.62       321
            INTP       0.68      0.74      0.71       391
            ISFJ       0.57      0.37      0.45        57
            ISFP       0.67      0.42      0.52        83
            ISTJ       0.62      0.48      0.54        54
            ISTP       0.68      0.51      0.58        92

       micro avg       0.65      0.65      0.65      2603
       macro avg       0.63      0.48      0.53      2603
    weighted avg       0.65      0.65      0.64      2603


## Naive Bayes

#### This algorithm was not a very good choice for our data and the results show it:

            Accuracy = 0.22051479062620052

                  precision    recall  f1-score   support

            ENFJ       0.00      0.00      0.00        53
            ENFP       0.00      0.00      0.00       195
            ENTJ       0.00      0.00      0.00        80
            ENTP       0.00      0.00      0.00       221
            ESFJ       0.00      0.00      0.00         8
            ESFP       0.00      0.00      0.00        10
            ESTJ       0.00      0.00      0.00        11
            ESTP       0.00      0.00      0.00        32
            INFJ       0.43      0.01      0.01       424
            INFP       0.22      1.00      0.36       571
            INTJ       0.00      0.00      0.00       321
            INTP       0.00      0.00      0.00       391
            ISFJ       0.00      0.00      0.00        57
            ISFP       0.00      0.00      0.00        83
            ISTJ       0.00      0.00      0.00        54
            ISTP       0.00      0.00      0.00        92

       micro avg       0.22      0.22      0.22      2603
       macro avg       0.04      0.06      0.02      2603
    weighted avg       0.12      0.22      0.08      2603



## Stochastic Gradient Descent

#### The accuracy here is the highest so far:

    Accuracy = 0.6699961582789089

                  precision    recall  f1-score   support

            ENFJ       0.75      0.17      0.28        53
            ENFP       0.66      0.60      0.63       195
            ENTJ       0.74      0.36      0.49        80
            ENTP       0.70      0.57      0.63       221
            ESFJ       0.67      0.25      0.36         8
            ESFP       0.50      0.10      0.17        10
            ESTJ       1.00      0.09      0.17        11
            ESTP       0.64      0.22      0.33        32
            INFJ       0.65      0.77      0.70       424
            INFP       0.69      0.85      0.76       571
            INTJ       0.66      0.65      0.66       321
            INTP       0.66      0.80      0.72       391
            ISFJ       0.74      0.35      0.48        57
            ISFP       0.66      0.37      0.48        83
            ISTJ       0.76      0.30      0.43        54
            ISTP       0.66      0.57      0.61        92

       micro avg       0.67      0.67      0.67      2603
       macro avg       0.70      0.44      0.49      2603
    weighted avg       0.68      0.67      0.65      2603

#### Let's also look at the confusion matrix:

    [[  9   5   0   3   0   0   0   0  11  16   1   4   0   0   1   3]
     [  0 117   2   5   0   0   0   1  25  23  13   7   0   1   0   1]
     [  2   7  29   6   0   0   0   1   4   7   9  12   1   1   0   1]
     [  0  10   2 126   0   0   0   0  19  20  18  20   0   6   0   0]
     [  0   0   0   0   2   0   0   0   0   1   0   4   1   0   0   0]
     [  0   2   0   1   1   1   0   0   2   1   0   2   0   0   0   0]
     [  0   0   0   4   0   0   1   0   1   4   1   0   0   0   0   0]
     [  0   1   1   4   0   0   0   7   6   1   1   4   1   0   0   6]
     [  0   6   2   8   0   0   0   0 327  51  13  15   1   0   0   1]
     [  1   9   1  10   0   0   0   0  18 484  13  23   0   4   2   6]
     [  0   3   1   2   0   1   0   0  37  26 208  38   1   1   1   2]
     [  0   3   1   7   0   0   0   1  21  26  14 314   0   0   0   4]
     [  0   4   0   0   0   0   0   0  12   6   6   8  20   1   0   0]
     [  0   2   0   0   0   0   0   0  10  25   4   7   1  31   1   2]
     [  0   6   0   0   0   0   0   0   5  10   8   7   1   0  16   1]
     [  0   3   0   3   0   0   0   1   7   5   5  14   0   2   0  52]]


## Logistic Regression

#### It did not out perform stochastic gradient descent. Here is the summary:

    Accuracy = 0.6300422589320015

                  precision    recall  f1-score   support

            ENFJ       0.69      0.45      0.55        53
            ENFP       0.60      0.59      0.60       195
            ENTJ       0.72      0.42      0.54        80
            ENTP       0.67      0.55      0.60       221
            ESFJ       0.50      0.38      0.43         8
            ESFP       0.50      0.10      0.17        10
            ESTJ       0.40      0.18      0.25        11
            ESTP       0.82      0.28      0.42        32
            INFJ       0.60      0.70      0.65       424
            INFP       0.66      0.78      0.71       571
            INTJ       0.59      0.58      0.58       321
            INTP       0.63      0.71      0.67       391
            ISFJ       0.67      0.46      0.54        57
            ISFP       0.55      0.37      0.45        83
            ISTJ       0.71      0.37      0.49        54
            ISTP       0.63      0.51      0.56        92

       micro avg       0.63      0.63      0.63      2603
       macro avg       0.62      0.47      0.51      2603
    weighted avg       0.63      0.63      0.62      2603

<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>


# Deep Learning

#### Let's create a Neural Network and see if we can get better results. The code for this can be found [here](Deep_Learning.ipynb)

    Accuracy = 0.9865539761813292

#### This is very impressive accuracy, let's look at the summary.

                  precision    recall  f1-score   support

               0       1.00      1.00      1.00        54
               1       1.00      1.00      1.00       214
               2       1.00      1.00      1.00        77
               3       1.00      1.00      1.00       186
               4       0.00      0.00      0.00        11
               5       0.00      0.00      0.00        15
               6       0.00      0.00      0.00         9
               7       1.00      1.00      1.00        21
               8       1.00      1.00      1.00       425
               9       0.98      1.00      0.99       538
              10       0.96      1.00      0.98       327
              11       0.97      1.00      0.99       408
              12       1.00      1.00      1.00        53
              13       1.00      1.00      1.00        88
              14       1.00      1.00      1.00        69
              15       1.00      1.00      1.00       108

       micro avg       0.99      0.99      0.99      2603
       macro avg       0.81      0.81      0.81      2603
    weighted avg       0.97      0.99      0.98      2603

#### Let's also take a look at the confusion matrix:

    [[ 54   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0]
     [  0 214   0   0   0   0   0   0   0   0   0   0   0   0   0   0]
     [  0   0  77   0   0   0   0   0   0   0   0   0   0   0   0   0]
     [  0   0   0 186   0   0   0   0   0   0   0   0   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0   0   0  11   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0   0  15   0   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0   9   0   0   0   0   0   0]
     [  0   0   0   0   0   0   0  21   0   0   0   0   0   0   0   0]
     [  0   0   0   0   0   0   0   0 425   0   0   0   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0 538   0   0   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0   0 327   0   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0   0   0 408   0   0   0   0]
     [  0   0   0   0   0   0   0   0   0   0   0   0  53   0   0   0]
     [  0   0   0   0   0   0   0   0   0   0   0   0   0  88   0   0]
     [  0   0   0   0   0   0   0   0   0   0   0   0   0   0  69   0]
     [  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0 108]]

<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>


# Emotional Analysis

Next let's dive into the emotions by each personality type. The code for this can be found [here](Emotional_Analysis).
![](img/emotionsByPersonality.png)

<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>


# WordClouds

#### Now let's go back to the data and see what we can derive
- Created a dictionary with all the observations of each Personality Type
 - After doing so it did not prove to be very useful
- Created another dictionary with high frequency words by Personality Type
 - This can help us make some word clouds but first we need to clean our data
- Created a list of the 30 most common words among all personality types
- Removed the words in that list from our dataset

Let's get a bit fancy, instead of the default wordclouds, we can use a template for them, since we are talking about the mind, let's use a head.

<img src="img/head2.png" alt="drawing" width="250"/>

After transforming this image, using the pillow library and numpy, we can use it to produce the following wordclouds.

|Extroverted|Introverted|
|:---:|:---:|
|ENFJ|INFJ|
|![](img/ENFJ.png)|![](img/INFJ.png)|
|ENFP|INFP|
|![](img/ENFP.png)|![](img/INFP.png)|
|ENTJ|INTJ|
|![](img/ENTJ.png)|![](img/INTJ.png)|
|ENTP|INTP|
|![](img/ENTP.png)|![](img/INTP.png)|
|ESFJ|ISFJ|
|![](img/ESFJ.png)|![](img/ISFJ.png)|
|ESFP|ISFP|
|![](img/ESFP.png)|![](img/ISFP.png)|
|ESTJ|ISTJ|
|![](img/ESTJ.png)|![](img/ISTJ.png)|
|ESTP|ISTP|
|![](img/ESTP.png)|![](img/ISTP.png)|

<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>


# Conclusion and Next Steps

- Took the datasets and performed Exploratory Data Analysis
- Created a data pipeline
- Built several models and picked stochastic gradient descent due to it's high accuracy and precision
- Built a Neural Network which improved gave great accuracy but was overfit to the over represnted classes
- Performed emotional analysis for each personality type
- Created Word Clouds based on the frequancy of words used by each personality type.
- Next step would be to gather data from another place like twitter or facebook and see if we can predict personalities based on that text

<a href="#Personality-Exploratory-Data-Analysis">Back to top</a>

```python

```