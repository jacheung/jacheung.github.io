---
title: 'Touch behavior'
subtitle: 'Using machine learning and experimental design to answer “what is the behavioral basis of touch localization?”'
date: 2020-04-30 00:00:00
featured_image: /images/landscapes/cortical-columns.jpg
---

![](/images/projects/localization/model-summary.png =300x450)
*Graphical summmary of the conclusions*

#### Primer
If you haven’t had a chance take a look at this short [primer](http://jacheung.com/blog/neuroscience-primer) I wrote about how neuroscientists study touch. It’ll help frame the ideas below. 

Since the brain and behavior are inextricably linked we can’t fully understand the brain unless we observe it driving a behavior. But before that, we must carefully dissect the behavior. One fundamental component in touch perception is knowing where objects are. This is part one of what I worked on in my PhD: “How do rodents use touch to locate objects?”

As an outline, we'll be covering: 
1. Background of localization 
2. Designing a localization behavior
3. Data acquisition pipeline
4. Using machine learning and experiments to define which features are most important for touch. 
5. Bringing it back to the brain. 

#### 1. What does it mean to dissect behavior? 

Over the past two decades, scientists have been trying to understand what features mice use to search for objects. Recognizing which feature is most important for localization helps us understand the brain in two ways:

 1. it provides a frame of reference for how important the brain representing some part of the outside world may be
 2. allows us to dissect the neural circuitry and algorithms deployed by the brain to generate behaviors.

In these past two decades, six different models for localization have been proposed and all have gaps in experimental support. I’ll go over each feature briefly but the below diagram should help.

![](/images/projects/localization/models.png)

* Roll angle: as the mouse whisks the whisker follicle rotates. During rotation the pattern of activation for mechanoreceptors provides a unique signature for each position. 
* Time from whisk onset: the time of touch is referenced to the time of whisk onset. Longer times are associated with farther positions.
* Time from cue onset: similar to the above, but the reference is the time from cue onset. 
* Number of touches: in this model, mice direct search to a particular position and consequently more touches are generated there. 
* Radial distance: mice measure the distance between the follicle and object by comparing the normal force relative to the angle. Since the axis of the pole is parallel to the midline of the mouse, farther positions are associated with longer radial distances.
* Hilbert recomposition: angle is the angle of the whisker orthogonal to the midline where 90 is parallel with and pointing towards the nose and 0 directly orthogonal to the midline. Using the Hilbert Transform, whisker angle can be decomposed to phase, amplitude, and midpoint. These features together can be combined to losslessly compute angle. 

#### 2. So how can we design a behavior that lets us evaluate object localization? 
Well to keep things simple, we expanded upon a state-of-the-art behavior and trained head-fixed mice to locate a pole using one whisker. This pole is controlled by a Zaber motor (which gives us XY positioning) and a pneumatic valve that pushes the pole up to be touchable by the mouse’s whiskers. Each trials lasts 4s. For each trial the pole comes up 0.5s from trial start, becomes touchable for approximately 2s, and then falls out of reach for the remainder of the trial. 

To see how precise mice could locate objects, we gave the mice reward if they correctly identified the pole in a close position. This is a go/no-go task where mice learn to lick in close positions and withhold licking in the far ones. 

Mice on average become expert after 2 weeks of training. To quantify precision we first look at psychometric curves (see below), which highlights the mouse’s performance varying with the location of the pole. Here we see that the probability of licking decreases as the pole location moves further away, which is what we expected. 

<div class="gallery" data-columns="2">
	<img src="/images/projects/localization/psychometric-curves.png">
	<img src="/images/projects/localization/localization-precision.png">
</div>


 We next quantify precision by evaluating lick probabilities equidistant from the center of the psychometric curves and plotting false-alarm rates vs hit rates. In signal detection theory, this plot is known as a receiver operating characteristic (ROC) curve. Instead of the usual curve fitting we plot a scatter to highlight the performance of individual mice. We see here that mice are still able to discriminate even within half-millimeter from the decision boundary. As a frame of reference your thumbnail is 2 centimeters meaning that mice can discriminate within 1/40th of your thumbnail!

#### 3. How do we gather the data required to answer which model mice use to locate objects? 

To test these models I devised a workflow to measure the behavior and extract the features relevant for each proposed model. This was achieved using high-speed imaging (1000 fps), motion-tracking, physics models, image classification, and time-series filtering (see diagram of the extract, transform, load [ETL] pipeline below). If we want to understand which feature is most important for perception, we’ll look at all the touch features before a decision is made (i.e. all touches before the first blue dot on the whisker traces). 

![](/images/projects/localization/workflow.png)

#### 4. Which feature is most important location perception? 

Simply we find that a combination of the touch number and mean whisker angle during touch was the simplest and best model at predicting behavior. Well that’s great but what tools did you use and how did you reach this conclusion? 

##### The machine learning models and evaluation metrics we used. 

As scientists, machine learning model interpretability is of utmost importance. High predictive accuracy isn't the only important metric. Interpretability is a foundation for building trust and understanding, both critical if we are going to make discoveries that press the boundary of science. At a very high level, we leveraged generalized linear models (GLM) and random forests, two powerful interpretable models, to see the predictive power of features in proposed models. Using GLMs, we can use the feature weights or converted odds ratios to evaluate feature importance. The same can be done with random forests and out of bag feature importance. 

At a low level, we used GLMs with a binomial link function and L1, lasso, regularization when multiple features were used. Lasso regularization not only increases generalizability of the model but also provides a simple model by highly weighting the most important predictors. Models were trained with five-fold cross validation and further validation of feature importance was tested using leave-one-out-covariance (LOCO).

The most conservative metric for evaluating ML models on imbalanced datasets is Matthew’s Correlation Coefficient (MCC). Compared to F1 scores or other precision/ recall metrics, MCC utilizes all outcomes in signal detection theory (i.e. hit, false alarm, miss, correct rejection). Here’s a summary of MCC values plotted against accuracy (%) for balanced vs imbalanced datasets.

##### What do the models tell us?  

Prediction with individual features and a combinatorial model using  interpretable models highlights the number of touches, radial distance at touch, and whisker angle at touch are candidate features for location perception in mice.

MCC FIGURE
Prediction with individual feature and using MCC as our metric we find that the number of touches, radial distance, and whisker angle at touch serve as the best predictors of choice. It should be noted that the combinatorial model using all features and the raw position of the pole serve as the upper ceiling of prediction using just sensory features. We acknowledge that there are many unmeasurable features that could drive choice such as motivational state or lapse in judgment.

WEIGHT FIGURE
By looking at the a combinatorial model using all features, both GLM variable weights and random forests out-of-bag error pointed to the same important features -  touch counts, radial distance, and angle. 

##### Well, all of these features look great but how did you simplify to two features? 

Models are not definitive. They only serve as a foundation for further hypothesis testing. To decipher between whisker angle and radial distance we devised a behavioral task that decomposed these two features. On one axis, radial distance was held constant and angle was modulated. On the other, angle was held constant and radial distance modulated. We tested expert mice on this task and found that accuracy when angle was held constant, plummeted to chance levels. Further, when radial distance was held constant accuracy matched performance in the original task. **These data taken together highlight that angle, NOT radial distance, is used by animals during localization.** (See figure set below, Left)

**Finally using both touch counts and whisker angle at touch, we find that these two features predict choice just as well as using all the features together. With these data and results, we conclude that whisker angle at touch and the number of touches together provide the simplest model to predict mouse localization strategies.** (See figure set below, Right)

<div class="gallery" data-columns="2">
	<img src="/images/projects/localization/task-decomposition.png">
	<img src="/images/projects/localization/combined-vs-simple.png">
</div>


#### 5. Wow, that was a lot. What does this all mean? 
We find two features (number of touches and whisker angle at touch) that are important for predicting choice. Understanding the features that predict the mouse’s location perception gives us a feature to look for in the brain. From the literature we find that these two features can be encoded in the brain. 

For the number of touches, cortex is a likely region to encode this via the number of spikes. What does this mean? Well first, it’s known that mammalian cortex is subdivided into six layers. Layer 4 in is the gateway to cortex and past works has found that this layer faithfully represents touch by spiking. This means that a downstream region that receives information from layer 4 could potentially learn that more spikes means more touches, thus providing a mechanism for how touch counts may be encoded. 

For the whisker angle at touch, no one has yet found a representation in or along the way to somatosensory cortex. Instead, they have found that regions such as thalamus and whisker motor cortex (two key regions that project to whisker somatosensory cortex) encode decomposed components of whisker angle. What does that mean? Angle can be decomposed to phase, amplitude, and midpoint and these features can together compute angle losslessly. Previous works have identified that midpoint and amplitude are encoded in projections from motor cortex and phase in projections from thalamus. One key region that integrates all this information is L5B. These neurons have apical dendrites that reach up to the top layers of cortex to receive information from motor cortex and basal dendrites to receive information directly from thalamus. 

If we can potentially find this representation of angle in the brain, we have the tools to manipulate these populations of neurons and bias perception. Understanding the circuit mechanisms that are involved in localizing objects could lead to potential therapeutics to restore the sense of touch. 

#### Resources
For the original publication see [here](https://www.sciencedirect.com/science/article/pii/S0960982219309480).  
For the code used to generate all figures see [here](https://github.com/jacheung/localization-behavior).  
For the original dataset in [MATLAB](https://www.dropbox.com/s/0oxsc4uix5zd9dl/BV.mat) and [Python](https://www.dropbox.com/s/0oxsc4uix5zd9dl/BV.mat).
