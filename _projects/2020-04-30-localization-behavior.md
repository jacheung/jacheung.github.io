---
title: 'Touch behavior'
subtitle: 'What are the features most important when locating objects with touch?'
date: 2020-04-30 00:00:00
featured_image: '/images/demo/demo-square.jpg'
---

#### Primer
If you haven’t had a chance take a look at this short primer I wrote about how neuroscientists study touch. It’ll help frame some of these ideas below. 

In the primer I’ve highlighted how mice serve as powerful models to study touch and the brain. Since the brain and behavior are inextricably linked we can’t fully understand the brain unless we see it in action driving a behavior. If we are to understand how the brain affects behavior we must first carefully dissect that behavior. One fundamental component in touch perception is knowing where objects are. This is part one of what I worked on in my PhD: “How do rodents use their whiskers to identify where objects are?”

#### What does it mean to dissect behavior? 

Over the past two decades, scientists have been trying to understand what features mice use to search for objects. Recognizing which feature is most important for localization helps us understand the brain in two ways: 1) it gives us as an idea of what representation to look for in the brain :2) whether manipulating that representation will have an effect on behavior

In these past two decades, six different models for localization have been proposed and all have gaps in experimental support. I’ll go over each feature briefly but the below diagram should help.

![](/images/projects/localization/models.png)

* Roll angle: as the mouse whisks the whisker follicle rotates. During rotation the pattern of activation for mechanoreceptors provides a unique signature for each position. 
* Time from whisk onset: the time of touch is referenced to the time of whisk onset. Longer times are associated with farther positions.
* Time from cue onset: similar to the above, but the reference is the time from cue onset. 
* Number of touches: in this model, mice direct search to a particular position and consequently more touches are generated there. 
* Radial distance: mice measure the distance between the follicle and object by comparing the normal force relative to the angle. Since the axis of the pole is parallel to the midline of the mouse, farther positions are associated with longer radial distances.
* Hilbert recomposition: angle is the angle of the whisker orthogonal to the midline where 90 is parallel with and pointing towards the nose and 0 directly orthogonal to the midline. Using the Hilbert Transform, whisker angle can be decomposed to phase, amplitude, and midpoint. These features together can be combined to losslessly compute angle. 

#### So how can we design a behavior that lets us evaluate object localization? 
Well to keep things simple, we expanded upon a state-of-the-art behavior and trained head-fixed mice to locate a pole using one whisker. This pole is controlled by a Zaber motor (which gives us XY positioning) and a pneumatic valve that pushes the pole up to be touchable by the mouse’s whiskers. Each trials lasts 4s. For each trial the pole comes up 0.5s from trial start, becomes touchable for approximately 2s, and then falls out of reach for the remainder of the trial. To see how precise mice could tell two positions apart, we gave the mice a water reward if they correctly identified whether the pole was in a close position. This is a go/no-go task where mice learn to lick in close positions and withhold licking in the far ones. 

Mice on average become expert after 2 weeks of training; where we defined expert as 75% or more correct across 200 trials. We can also look at the psychometric curves which highlights the mouse’s performance varying with the location of the object. Here we see that the probability of licking (y-axis) decreases as the pole location moves further away, which is what we expected. 

<div class="gallery" data-columns="2">
	<img src="/images/projects/localization/psychometric-curves.png">
	<img src="/images/projects/localization/localization-precision.png">
</div>


We can evaluate the localization precision by mice by evaluating trials equidistant from the center and plotting false-alarm rates vs hit rates. In signal detection theory, this plot is known as a receiver operating characteristic (ROC) curve. Instead of the usual curve fitting we plot a scatter to highlight the performance of individual mice. We see here that mice are still able to discriminate even within half-millimeter from the decision boundary. As a frame of reference your thumbnail is 2 centimeters meaning that mice can discriminate within 1/40th of your thumbnail!

#### How do we gather the data required to answer which model mice use to locate objects? 

To test these models I devised a workflow to measure the behavior and extract the features relevant for each proposed model. This was achieved using high-speed imaging (1000 fps), motion-tracking, physics models, image classification, and time-series filtering (see diagram of the extract, transform, load [ETL] pipeline below). If we want to understand which feature is most important for perception, we’ll look at all the touch features before a decision is made (i.e. all touches before the first blue dot on the whisker traces). 

![](/images/projects/localization/workflow.png)

#### Which feature is most important location perception? 

Simply we find that a combination of the touch number and mean whisker angle during touch was the simplest and best model at predicting behavior. Well that’s great but what tools did you use and how did you reach this conclusion? 

##### The machine learning models and evaluation metrics we used. 

As scientists, machine learning model interpretability is of utmost importance. High predictive accuracy isn't the only important metric. Interpretability is a foundation for building trust and understanding, both critical if we are going to make discoveries that press the boundary of science. At a very high level, we leveraged generalized linear models (GLM) and random forests, two powerful interpretable models, to see the predictive power of features in proposed models. Using GLMs, we can use the feature weights or converted odds ratios to evaluate feature importance. The same can be done with random forests and out of bag feature importance. 

At a low level, we used GLMs with a binomial link function and L1, lasso, regularization when multiple features were used. Lasso regularization not only increases generalizability of the model but also provides a simple model by highly weighting the most important predictors. Models were trained with five-fold cross validation and further validation of feature importance was tested using leave-one-out-covariance (LOCO).

The most conservative metric for evaluating ML models on imbalanced datasets is Matthew’s Correlation Coefficient (MCC). Compared to F1 scores or other precision/ recall metrics, MCC utilizes all outcomes in signal detection theory (i.e. hit, false alarm, miss, correct rejection). Here’s a summary of MCC values plotted against accuracy (%) for balanced vs imbalanced datasets.

##### What do the models tell us?  

The prediction with individual features and a combinatorial model using all features in an interpretable model gives us confidence that the number of touches, radial distance, and whisker angle at touch are strong candidate features that mice use to localize objects. 

MCC FIGURE
Looking at each individual feature and using MCC as our metric I find that the number of touches, radial distance, and whisker angle at touch serve as the best predictors of choice. It should be noted that the combinatorial model using all features and the raw position of the pole serve as the upper ceiling of prediction using just sensory features. We acknowledge that there are many unmeasurable features that could drive choice such as motivational state or lapse in judgment.

WEIGHT FIGURE
By looking at the a combinatorial model using all features, both GLM variable weights and random forests out-of-bag error pointed to the same important features -  touch counts, radial distance, and angle. 

##### Well, all of these features look great but how did you simplify to two features? 

Models are not definitive. They only serve as a foundation for further hypothesis testing. To decipher between whisker angle and radial distance we devised a behavioral task that decomposed these two features. On one axis, radial distance was held constant and angle was modulated. On the other, angle was held constant and radial distance modulated. We tested expert mice on this task and found that accuracy when angle was held constant, plummeted to chance levels. Further, when radial distance was held constant accuracy matched performance in the original task. **These data taken together highlight that angle, NOT radial distance, is used by animals during localization.**

**Finally using both touch counts and whisker angle at touch, we find that these two features predict choice just as well as using all the features together. With these data and results, we conclude that whisker angle at touch and the number of touches together provide the simplest model to predict mouse localization strategies.**

#### Wow, that was a lot. What does this all mean? 
We find two features (number of touches and whisker angle at touch) that are important for predicting choice. Understanding the features that predict the mouse’s location perception gives us a feature to look for in the brain. From the literature we find that these two features can be encoded in the brain. 

For the number of touches, cortex is a likely region to encode this via the number of spikes. What does this mean? Well first, it’s known that mammalian cortex is subdivided into six layers. Layer 4 in is the gateway to cortex and past works has found that this layer faithfully represents touch by spiking. This means that a downstream region that receives information from layer 4 could potentially learn that more spikes means more touches, thus providing a mechanism for how touch counts may be encoded. 

For the whisker angle at touch, no one has yet found a representation in or along the way to somatosensory cortex. Instead, they have found that regions such as thalamus and whisker motor cortex (two key regions that project to whisker somatosensory cortex) encode decomposed components of whisker angle. What does that mean? Angle can be decomposed to phase, amplitude, and midpoint and these features can together compute angle losslessly. Previous works have identified that midpoint and amplitude are encoded in projections from motor cortex and phase in projections from thalamus. One key region that integrates all this information is L5B. These neurons have apical dendrites that reach up to the top layers of cortex to receive information from motor cortex and basal dendrites to receive information directly from thalamus. 

If we can potentially find this representation of angle in the brain, we have the tools to manipulate these populations of neurons and bias perception. Understanding the circuit mechanisms that are involved in localizing objects could lead to potential therapeutics to restore the sense of touch. 

