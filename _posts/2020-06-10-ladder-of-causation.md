---
layout: post
title: Initial thoughts on causal inference.
subtitle: Thoughts on building better AI. 
tags: [AI]
comments: true
featured_image: '/images/latest/path-analysis/top-down.JPG'
---

On my quest to build smarter AI systems we can trust and understand, I’ve found myself breezing through Judea Pearl’s book “The Book of Why” to understand causal inference. Pearl pitches the use of causal models as a bridge from objective data to subjective reasoning. If machines were able to build causal (or what Pearl refers to as subjective hypotheses) models of the world, they would not require massive amounts of data to train and could extrapolate experiences to unknown instances. In a sense, machines could creatively make hypothesis about the world and test them and explain to humans, via a causal model, why a certain decision was predicted. 

Below are a collection of initial thoughts. 

### The ladder of causation 

Pearl first frames the benefits of causal inference via the Ladder of Causation. 

1. Association - At the lowest level (i.e. rung one) there is association (linking A -> B; seeing and observing). This is the limit of current machine learning models. ML models linearly/non-linearly correlate A -> B. 
2. Intervention (doing/intervening) what if I do? How? 
3. Counterfactuals (imagining a counter-scenario, understanding) what if I had done? Why? 

Pearl agues that data by itself is dumb. At the crux of his argument, data is unable to surpass rung one of the ladder. Machines using just data are unable to answer questions “what would happen if I did this” in Rung Two and importantly “what if i had done this instead?” in Rung Three. In a sense, machines are unable to predict cases that deviate too far from what they have seen. 

> “While probabilities encode our beliefs about a static world, causality tells us whether and how probabilities change when the world changes, be it by intervention or act of imagination.”  
-Judea Pearl

### A tool to quantify causation - path analysis

Well how do we move from these nebulous ideas of causal models to quantifiable metrics. In the past, some have proposed the use of Granger Causality or Vector Autoregression as a tool to understand causality. However they both lack… Pearl proposes the use of path analyses (i.e. structural equation modeling) as a quantitative metric for linking correlation to causation. 

Now path analysis wasn’t Pearl idea but a revival of Sewall Wright’s ideas back in 1920. Sewall Wright in his quest to try breed a full white or full colored guinea pig (even by inbreeding which Mendelian genetics thought that a trait could be fixed by multiple generations) revived causal thought. His failure to breed a full colored/white guinea pig caused him to think that there were other forces such as Developmental (D) and Environmental (E) that was built on top of Genetics (G) to generate color patterns. To express his thoughts, Sewall Wright devised a path diagram (1920) that illustrated factors leading to coat color. This path diagram was the first bridge ever built between causality and probability (crossing of rung two and rung one of the ladder of causality) to be published in a time when correlation was thought to be the only statistical measure that mattered. The path coefficients themselves represent the strength of the causal effect linking one variable to another. Path coefficients are not correlation values, but how are they calculated and how do they relate to correlation coefficients?

![](/images/latest/path-analysis/guinea-pig-path-analysis.png)

Below are the mathematics of path analysis by Sewall Wright from 1921 in his study of guinea pig weight. Wright had one goal: to identify *p* (the effect of gestation period on birth weight). Wright measured 5.66 grams gained per day of gestation. However, this number doesnt give the direct effect *p* but the correlation biased by other factors such as Litter Size. To identify *p* one can perform algebraic magic via calculations of (P, X), (L, X), and (L, P) which would return 3 equations and solve for p, l’, and l * q. Given this, the true weight gained per day of gestation would be 3.34 grams. This is amazing because given one single correlation value, 5.66 grams, and a hypothesized causal diagram of the world one would be able to distill the causal effect of one variable on another! 

![](/images/latest/path-analysis/judea-pearl-path.png)

> “Causal analysis is emphatically not just about data; in causal analysis we must incorporate some understanding of the process that produces the data, and that we get something that was not in the data to begin with…. Path analysis should be based on the users’s personal understanding of causal processes, reflected in the causal diagram. It can’t be reduced to mechanical routines, such as those laid out in statistics manual.”  
-Judea Pearl 

 What Pearl is saying is path analysis requires scientific thinking to identify causality between variables. Look at the world around you or the question you are trying to answer. What have you observed and how can you intuit a causal diagram/hypothesis about the cause and effect? Once you have these diagrams, you can then use correlations between variables and basic algebra to quantify the causal affect one variable has on another. 

One caveat of path analysis is that it assumes linearity between variables. However, the world may not operate linearly. Pearl and Versa identify a [general non-linear theory](https://www.sciencedirect.com/science/article/pii/S0049237X06800741) to address this. 

### Testing path analysis

Much to my surprise, I realized that I’ve skimmed the surface of causal diagrams but never had the quantitative tools to link the correlations I saw to causation. Back in 2018, before the submission of my [first peer reviewed article](http://jacheung.com/images/localization-behavior.pdf), I proposed the diagram below in an internal meeting. It was a causal diagram based on watching thousands of performed trials and parsing through choice probabilities across dozens of animals. Looking back on it now, I’d summarize and given the correlation values I’ve seen and performing cursory inspection (or  “algebraic magic” as Pearl likes to call it), I could calculate the causal effect of specific features on driving location perception. *voila*.

![](/images/latest/path-analysis/FigCartoonSummary_V2.png)

### Final thoughts

I am by no means an expert on causal inference and have yet to uncover all the arguments Pearl makes. However, I am in favor of Pearl's line of thinking. Causal inference requires us to employ scientific thinking. It requires us to take a step back, observe the world, and hypothesize a testable model for how the world may operate. It leads us to see beyond data as a crystal ball and recognize that it is purely a tool for understanding the world. This is purely anecdotal but our brains don't purely use data to estimate the world around us. Instead we use a combination of data and a modeled expectation of world, so why wouldn't we build machines that do that also? 

Back to reading!