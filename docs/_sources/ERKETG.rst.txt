.. _ERKETG:

Deciphering the History of ERK Activity from Fixed-Cell Immunofluorescence Measurements
=========================================================================================

Abstract
--------
The Ras/ERK pathway drives cell proliferation and other oncogenic behaviors, and quantifying its activity in situ is of high interest in cancer diagnosis and therapy. Pathway activation is often assayed by measuring phosphorylated ERK. However, this form of measurement overlooks dynamic aspects of signaling that can only be observed over time. In this study, we combine a live, single-cell ERK biosensor approach with multiplexed immunofluorescence staining of downstream target proteins to ask how well immunostaining captures the dynamic history of ERK activity. Combining linear regression, machine learning, and differential equation models, we develop an interpretive framework for immunostains, in which Fra-1 and pRb levels imply long term activation of ERK signaling, while Egr-1 and c-Myc indicate recent activation. We show that this framework can distinguish different classes of ERK dynamics within a heterogeneous population, providing a tool for annotating ERK dynamics within fixed tissues.

A dataset linking live-cell ERK activity to ERK target immunofluorescence
--------------------------------------------------------------------------  
To create a dataset that enables correlation of ERK activation to downstream target expression and modification, we first collected live ERK activity measurements in response to differential activation of the RAS/MAP Kinase pathway. We used EKAR 3.5, a calibrated, FRET-based biosensor of ERK activity, to measure single-cell activation in MCF10A mammary epithelial cells. With a series of Epidermal Growth Factor (EGF) concentrations, ERK activity was stimulated in a dose-dependent manner 

.. figure:: images/P1_schema.png

    Experimental setup

.. figure:: images/P2.png

    Single-cell ERK actvity measurements.

Immediately following live-cell data collection, we fixed the cells, and conducted cyclic immunofluorescence (4i) staining to measure levels of eight targets downstream of ERK.

.. figure:: images/P3.png

    Quantification of ERK Target protein expression via Immunofluorescence.

We then analyzed the correlation between ERK activity and the expression of each target. To link live-cell ERK activity measurements with the respective 4i data for each cell, we aligned the corresponding image datasets and generated a heatmap arranged by the mean ERK activity measurement in each cell. While both ERK activity and 4i targets were variable across the data set, most of the 4i targets exhibited some discernible correlation with mean ERK activity, which was especially strong for Fra-1 and pRb.

.. figure:: images/P4.png

    Single-cell heatmap of ERK activity coupled with protein expression.

Regression modeling
----------------------------------------------------------------------------------

For a rigorous statistical analysis of the relationship between ERK activity and ETG expression, we performed cross-validated linear regression using the 4i measurements as predictors and ERK pulse features as response variables. We first created single predictor models to assess how well each target individually predicts each ERK feature in an individual cell. 
Analysis of the variance explained (R2) for each model confirms the results from the Pearson correlation analysis. Fra-1 and pRb best predict the sum of duration of ERK pulses (R2 = 0.42, 0.31, respectively), and the average ERK activity in each cell (R2 = 0.28 and 0.23).
These results suggest the duration of ERK activation seems to have a stronger influence on gene expression than the strength of the activation.

To assess whether the prediction models can be improved by considering multiple stains simultaneously, we generated several multiple linear regression (MLR) models using all 4i measurements as predictors at once. The ERK parameter with the highest variance explained was the sum of duration (R2 = 0.52). 
The models for derivative benefited the most from the multivariable models, however, they still only explained 33% of the variation in the data. c-Jun again served as a negative control, as those models did not explain any of the variation in ERK activity.
We then investigated which antibody combinations are most important in the MLR models. For each ERK feature, we created models that successively added predictors, and measured the resulting R2, and test-set error for each new predictor. 
For most ERK features, we found that the maximum R2 values can be achieved with just 2 to 3 predictors, where adding Fra-1 and pRb typically caused the highest improvement in R2 values and decrease in test-set error. The best model for the average derivative of ERK had a similar R2 with the model for the ERK mean (R2 = 0.36, 0.33, respectively). The main distinction for the average derivative model is the strong contribution from Egr-1.

.. figure:: images/P5.png

    Regression analysis of the relationship between ERK and ETGs

Neural network-based models
----------------------------------------------------------------------------------------------------------

While the previous models of featurized ERK activity provide interpretable correlations that help to understand the underlying biological process, they assume linearity and may not capture more complex relationships in the data. Additionally, some ERK parameters are correlated with each other, and other features of the time series may be missed. 
To examine the importance of the timing of ERK activation and identify which timepoints have he greatest impact on final expression levels, we trained a convolutional neural network (CNN) to use the ERK activity time series to predict expression levels of each ERK target in individual cells. 
As a comparison to the CNN, we also fit linear regression models (TS linear) using the values at each timepoint of the ERK time series as individual variables to predict final ERK target levels. 

We compared the performance of these time series-based models with that of ERK dynamics feature based models (Featurized linear). These feature-based models used all nine featurized ERK measurements (i.e. mean/duration/frequency) to predict the expression of each ERK target.
We found that the CNN achieved the highest performance in predicting all ERK targets, except for pERK. To account for overfitting, we calculated the mean squared error (MSE) on unseen data (test set) and the CNN exhibited the least error for all targets, except for pERK. Although the CNN yielded better performance for most targets, a significant amount of variance is still not captured by the model. 
Notably, the CNN models for Egr-1 and pRb explained much more variance than linear regression models of other targets, implying that Egr-1 and pRb likely respond to ERK activation with significant non-linearity. 
Finally, for many 4i targets, the featurized linear models underperformed the other two methods, both in R2 and test set error, indicating that the featurization method often does not capture important aspects of ERK signaling
that influence gene expression.

.. figure:: images/P6.png

    Comparison of CNN and regression models.

Classification models
-------------------------------------------------------------------------------------------------------------

Thus far, we have trained models that predict several continuous variables that represent ERK history; however, the application of these models is limited by the challenge of concurrently visualizing the predictions. 
Therefore, we demonstrate here how spatiotemporal ERK predictions can be represented in a concise and intuitive manner. To do so, we first used k-means clustering to group cells into similar response classes, or prototypes, of ERK activity. We clustered cells into five classes: low activation (cluster 1), recent deactivation (cluster 2), long term activation (cluster 3), mid-term activation (cluster 4), and recent activation (cluster 5) 
Analysis of the 4i target expression levels in each cluster was consistent with our previous statistical models. Long-term activation led to the highest expression of pERK, Fra-1, and pRb, while low activation displayed the lowest for all targets. Cells with recent activation highly expressed Egr-1 and c-Myc.

We next trained a decision tree classifier that predicts prototypes of ERK signaling history using ERK target expression levels. The overall prediction accuracy of our model was 51% (compared to 20% for random selection), while individual class predictions varied in accuracy. Long-term activation class predictions were the most accurate (64%), and mid-term activation classifications were the least accurate (36%). 
These findings indicate that long term and recent activation result in distinct patterns of the expressed genes we measured, while mid-term activation produces the highest variability in gene expression.

Finally, to simulate a potential use case with fixed tissue samples, we then used our classifier to predict ERK activity classes, and therefore histories, in cells from a single well in our dataset. Our analysis effectively quantifies the distinctiveness in gene expression associated with different ERK signaling prototypes and illustrates the utility of ETG stains in predicting the spatiotemporal signaling history of individual cells.

.. figure:: images/P7.png
    :scale: 25 %


.. figure:: images/P8.png

        Inferring ERK signaling histories

Discussion
-----------
Here, we provide proof of principle that end-point ETG staining can be used to infer key aspects of long-term ERK activity within fixed cell samples. While differences in ETG activation by ERK were previously known, our analysis formalizes these differences and shows how quantitative models can be used to infer ERK’s activity history with single-cell resolution. The ETG measurements in these experiments provide information about two broad types of ERK behavior, long-term and short-term activation. Additionally, our model analysis of simulated ETGs demonstrates that additional measurements could even more finely resolve signaling patterns, such as intermittent pulses. 
The experimental and biological limits of these predictions remain to be established; however, this model framework can be used to estimate properties of ETGs that would optimally improve the measurement set.
