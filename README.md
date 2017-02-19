# levelfindengine_poreSEQ
Use Bayesian model to extract key DNA base features (current levels and fluctuation information) from raw nanopore sequencing data.

##Overview:

Nanopore DNA sequencing data is time series current signal rather than the traditional fluorescent signal. Findengine aiming to DNA bases current levels is the first critical step for analyzing raw nanopore DNA sequencing data. Mining all the useful features from nanopore sequencing raw data to help distinguish DNA bases is still ongoing research with substantial challenges. So far current level is considered as the most straight-forwards feature to identify DNA bases. Our group most recent research has found that not only the current level, but the current fluctuation within the current level also strongly correlated to DNA bases identification. Therefore, the goal of this DNA "events" findengine must include both the current mean levels and current fluctuation magnitude from the time series data. Obtaining accurate current levels, fluctuation and duration features can be extremely helpful and meaningful for further sequencing analysis, like alignment work.


##Modeling and coding procedure:

In this code, I use Bayesian statistics model to find DNA bases events. Similar analysis has been implemented in previous nanopore sequencing [publication](http://www.nature.com/nbt/journal/v32/n8/full/nbt.2950.html). The main advantage for the Bayesian model in this analysis is it only requires one general assumption: All noisy current segments for the nanopore sequencing data can be respectively characterized as a cluster of normally distributed datasets. Each Gaussian distribution could have its own mean value (mean current level) and variance (current fluctuation breadth). The Bayesian model here is to specifically predict whether the segment of noisy current data belongs to a single Gaussian distribution or not, written as Posterior P(M|D). D is the time series current data for the whole segment. M is the model that considers the whole segment data belong to one Gaussian. P(M|D)=P(M)*P(D|M). The likelihood P(D|M) on the time series data has its mathematical definition, shown on this [blog](https://gasstationwithoutpumps.wordpress.com/2014/06/17/segmenting-noisy-signals-revisited/). A pivot point make the probability evaluation on the segment split into half. An intuitive explanation is that one Gaussian distribution makes the likelihood close to 1, and likelihood value is insensitive to pivot point position (the analysis of first/second half data is the same.); On the other hand, more than one Gaussian distributions in the segment make the likelihood close to 0. And the smallest likelihood value will appear at the transition point between different levels. Then, the question about finding the level transition point become a minimum value searching question for likelihood or posterior, since the prior P(M) is constant for the data. Such minimum value searching process can be done recursively until all levels transition positions are found. About the termination of the recursion, P(M) provide a good reference. In my analysis, the logP(M|D) usually give ~-10 value, if the whole segment is one Gaussian distribution. Such logP(M|D) value is close to log(P(M)). I use 2P(M) as the termination of recursion. The mathematic definition of P(M) can be found in the same blog mentioned before. However, if the filter involving into signal processing, the P(M) calculation is far more complicated than the definition in that poster blog. I recommend doing a quick test of log(P(D|M) on a single Gaussian segment. Using P(D|M) value to estimate P(M). It is a practical alternative solution in my case. 
The result of the level findengine is shown in the figure below:
![levelfindengine](https://cloud.githubusercontent.com/assets/19654472/23100156/a992799c-f647-11e6-87f5-9cce675d8e6c.png)
