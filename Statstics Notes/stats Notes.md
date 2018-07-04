
#### What are the strengths of AUC 

Its two great strengths are, first, that AUC results do not change with changes in the incidents of the actual condition, nor is AUC affected by changes in the relative cost of the two different types of binary classification errors, false positives and false negatives. Therefore, when either future incidents or the cost of classification errors or both are unstable or cannot be known, the AUC is generally the best possible performance metric available. 

An AUC of one would be classification with zero errors, a theoretical ideal that no one expects you to meet. A normal good to very good area under the curve is typically in the .65 to .85 range.


In the example spreadsheet, there are seven events numbered one through seven. In column B, rows 20 through 26. Each event must be assigned a score and an outcome if we are to generate an ROC curve. 

Our model is a procedure or algorithm that inputs known data about an event and outputs a single score. 



#### Stastics vs Machine Learning 
Many methods from statistics and machine learning (ML) may, in principle, be used for both prediction and inference. However, statistical methods have a long-standing focus on inference, which is achieved through the creation and fitting of a project-specific probability model. The model allows us to compute a quantitative measure of confidence that a discovered relationship describes a 'true' effect that is unlikely to result from noise. Furthermore, if enough data are available, we can explicitly verify assumptions (e.g., equal variance) and refine the specified model, if needed.

By contrast, ML concentrates on prediction by using general-purpose learning algorithms to find patterns in often rich and unwieldy data1,2. ML methods are particularly helpful when one is dealing with 'wide data', where the number of input variables exceeds the number of subjects, in contrast to 'long data', where the number of subjects is greater than that of input variables. ML makes minimal assumptions about the data-generating systems; they can be effective even when the data are gathered without a carefully controlled experimental design and in the presence of complicated nonlinear interactions. However, despite convincing prediction results, the lack of an explicit model can make ML solutions difficult to directly relate to existing biological knowledge.

........

The boundary between statistical inference and ML is subject to debate1—some methods fall squarely into one or the other domain, but many are used in both. For example, the bootstrap6 method can be used for statistical inference but also serves as the basis for ensemble methods, such as the RF algorithm. Statistics requires us to choose a model that incorporates our knowledge of the system, and ML requires us to choose a predictive algorithm by relying on its empirical capabilities. Justification for an inference model typically rests on whether we feel it adequately captures the essence of the system. The choice of pattern-learning algorithms often depends on measures of past performance in similar scenarios. Inference and ML are complementary in pointing us to biologically meaningful conclusions.

https://www.nature.com/articles/nmeth.4642
https://www.analyticsvidhya.com/blog/2015/07/difference-machine-learning-statistical-modeling/
https://www.svds.com/machine-learning-vs-statistics/

#### Pearson Coefficient
https://statistics.laerd.com/statistical-guides/pearson-correlation-coefficient-statistical-guide.php



#### What is the best way to calculate the AUC of a ROC curve?
#### How to Calculate AUC on c-statistics? 

https://stats.stackexchange.com/questions/145566/how-to-calculate-area-under-the-curve-auc-or-the-c-statistic-by-hand

They have the following table of disease status and test result (corresponding to, for example, the estimated risk from a logistic model). The first number on the right is the number of patients with true disease status ‘normal’ and the second number is the number of patients with true disease status ‘abnormal’:

(1) Definitely normal: 33/3
(2) Probably normal: 6/2
(3) Questionable: 6/2
(4) Probably abnormal: 11/11
(5) Definitely abnormal: 2/33

So there are in total 58 ‘normal’ patients and ‘51’ abnormal ones. We see that when the predictor is 1, ‘Definitely normal’, the patient is usually normal (true for 33 of the 36 patients), and when it is 5, ‘Definitely abnormal’ the patients is usually abnormal (true for 33 of the 35 patients), so the predictor makes sense. But how should we judge a patient with a score of 2, 3, or 4? What we set our cutoff for judging a patients as abnormal or normal to determines the sensitivity and specificity of the resulting test.

Sensitivity and specificity
We can calculate the estimated sensitivity and specificity for different cutoffs. (I’ll just write ‘sensitivity’ and ‘specificity’ from now on, letting the estimated nature of the values be implicit.)

If we choose our cutoff so that we classify all the patients as abnormal, no matter what their test results says (i.e., we choose the cutoff 1+), we will get a sensitivity of 51/51 = 1. The specificity will be 0/58 = 0. Doesn’t sound so good.

OK, so let’s choose a less strict cutoff. We only classify patients as abnormal if they have a test result of 2 or higher. We then miss 3 abnormal patients, and have a sensitivity of 48/51 = 0.94. But we have a much increased specificity, of 33/58 = 0.57.

We can now continue this, choosing various cutoffs (3, 4, 5, >5). (In the last case, we won’t classify any patients as abnormal, even if they have the highest possible test score of 5.)

The ROC curve
If we do this for all possible cutoffs, and the plot the sensitivity against 1 minus the specificity, we get the ROC curve. We can use the following R code:

# Data
norm     = rep(1:5, times=c(33,6,6,11,2))
abnorm   = rep(1:5, times=c(3,2,2,11,33))
testres  = c(abnorm,norm)
truestat = c(rep(1,length(abnorm)), rep(0,length(norm)))

# Summary table (Table I in the paper)
( tab=as.matrix(table(truestat, testres)) )
The output is:

        testres
truestat  1  2  3  4  5
       0 33  6  6 11  2
       1  3  2  2 11 33
We can calculate various statistics:

( tot=colSums(tab) )                            # Number of patients w/ each test result
( truepos=unname(rev(cumsum(rev(tab[2,])))) )   # Number of true positives
( falsepos=unname(rev(cumsum(rev(tab[1,])))) )  # Number of false positives
( totpos=sum(tab[2,]) )                         # The total number of positives (one number)
( totneg=sum(tab[1,]) )                         # The total number of negatives (one number)
(sens=truepos/totpos)                           # Sensitivity (fraction true positives)
(omspec=falsepos/totneg)                        # 1 − specificity (false positives)
sens=c(sens,0); omspec=c(omspec,0)              # Numbers when we classify all as normal
And using this, we can plot the (estimated) ROC curve:

plot(omspec, sens, type="b", xlim=c(0,1), ylim=c(0,1), lwd=2,
     xlab="1 − specificity", ylab="Sensitivity") # perhaps with xaxs="i"
grid()
abline(0,1, col="red", lty=2)
AUC curve

Manually calculating the AUC
We can very easily calculate the area under the ROC curve, using the formula for the area of a trapezoid:

height = (sens[-1]+sens[-length(sens)])/2
width = -diff(omspec) # = diff(rev(omspec))
sum(height*width)
The result is 0.8931711.

A concordance measure
The AUC can also be seen as a concordance measure. If we take all possible pairs of patients where one is normal and the other is abnormal, we can calculate how frequently it’s the abnormal one that has the highest (most ‘abnormal-looking’) test result (if they have the same value, we count that this as ‘half a victory’):

o = outer(abnorm, norm, "-")
mean((o>0) + .5*(o==0))
The answer is again 0.8931711, the area under the ROC curve. This will always be the case.

A graphical view of concordance
As pointed out by Harrell in his answer, this also has a graphical interpretation. Let’s plot test score (risk estimate) on the y-axis and true disease status on the x-axis (here with some jittering, to show overlapping points):

plot(jitter(truestat,.2), jitter(testres,.8), las=1,
     xlab="True disease status", ylab="Test score")
Scatter plot of risk score against true disease status.

Let us now draw a line between each point on the left (a ‘normal’ patient) and each point on the right (an ‘abnormal’ patient). The proportion of lines with a positive slope (i.e., the proportion of concordant pairs) is the concordance index (flat lines count as ‘50% concordance’).

It’s a bit difficult to visualise the actual lines for this example, due to the number of ties (equal risk score), but with some jittering and transparency we can get a reasonable plot:

d = cbind(x_norm=0, x_abnorm=1, expand.grid(y_norm=norm, y_abnorm=abnorm))
library(ggplot2)
ggplot(d, aes(x=x_norm, xend=x_abnorm, y=y_norm, yend=y_abnorm)) +
  geom_segment(colour="#ff000006",
               position=position_jitter(width=0, height=.1)) +
  xlab("True disease status") + ylab("Test\nscore") +
  theme_light()  + theme(axis.title.y=element_text(angle=0))
Scatter plot of risk score against true disease status, with lines between all possible observation pairs.

We see that most of the lines slope upwards, so the concordance index will be high. We also see the contribution to the index from each type of observation pair. Most of it comes from normal patients with a risk score of 1 paired with abnormal patients with a risk score of 5 (1–5 pairs), but quite a lot also comes from 1–4 pairs and 4–5 pairs. And it’s very easy to calculate the actual concordance index based on the slope definition:

d = transform(d, slope=(y_norm-y_abnorm)/(x_norm-x_abnorm))
mean((d$slope > 0) + .5*(d$slope==0))
The answer is again 0.8931711, i.e., the AUC.

The Wilcoxon–Mann–Whitney test
There is a close connection between the concordance measure and the Wilcoxon–Mann–Whitney test. Actually, the latter tests if the probability of concordance (i.e., that it’s the abnormal patient in a random normal–abnormal pair that will have the most ‘abnormal-looking’ test result) is exactly 0.5. And its test statistic is just a simple transformation of the estimated concordance probability:

> ( wi = wilcox.test(abnorm,norm) )
    Wilcoxon rank sum test with continuity correction

data:  abnorm and norm
W = 2642, p-value = 1.944e-13
alternative hypothesis: true location shift is not equal to 0
The test statistic (W = 2642) counts the number of concordant pairs. If we divide it by the number of possible pairs, we get a familar number:

w = wi$statistic
w/(length(abnorm)*length(norm))
Yes, it’s 0.8931711, the area under the ROC curve.

Easier ways to calculate the AUC (in R)
But let’s make life easier for ourselves. There are various packages that calculate the AUC for us automatically.

The Epi package
The Epi package creates a nice ROC curve with various statistics (including the AUC) embedded:

library(Epi)
ROC(testres, truestat) # also try adding plot="sp"
ROC curve from the Epi package

The pROC package
I also like the pROC package, since it can smooth the ROC estimate (and calculate an AUC estimate based on the smoothed ROC):

ROC curve (unsmoothed and smoothed) from the pROC package

(The red line is the original ROC, and the black line is the smoothed ROC. Also note the default 1:1 aspect ratio. It makes sense to use this, since both the sensitivity and specificity has a 0–1 range.)

The estimated AUC from the smoothed ROC is 0.9107, similar to, but slightly larger than, the AUC from the unsmoothed ROC (if you look at the figure, you can easily see why it’s larger). (Though we really have too few possible distinct test result values to calculate a smooth AUC).

The rms package
Harrell’s rms package can calculate various related concordance statistics using the rcorr.cens() function. The C Index in its output is the AUC:

> library(rms)
> rcorr.cens(testres,truestat)[1]
  C Index 
0.8931711
The caTools package
Finally, we have the caTools package and its colAUC() function. It has a few advantages over other packages (mainly speed and the ability to work with multi-dimensional data – see ?colAUC) that can sometimes be helpful. But of course it gives the same answer as we have calculated over and over:

library(caTools)
colAUC(testres, truestat, plotROC=TRUE)
             [,1]
0 vs. 1 0.8931711
ROC curve from the caTools package

Final words
Many people seem to think that the AUC tells us how ‘good’ a test is. And some people think that the AUC is the probability that the test will correctly classify a patient. It is not. As you can see from the above example and calculations, the AUC tells us something about a family of tests, one test for each possible cutoff.

And the AUC is calculated based on cutoffs one would never use in practice. Why should we care about the sensitivity and specificity of ‘nonsensical’ cutoff values? Still, that’s what the AUC is (partially) based on. (Of course, if the AUC is very close to 1, almost every possible test will have great discriminatory power, and we would all be very happy.)

The ‘random normal–abnormal’ pair interpretation of the AUC is nice (and can be extended, for instance to survival models, where we see if its the person with the highest (relative) hazard that dies the earliest). But one would never use it in practice. It’s a rare case where one knows one has one healthy and one ill person, doesn’t know which person is the ill one, and must decide which of them to treat. (In any case, the decision is easy; treat the one with the highest estimated risk.)

So I think studying the actual ROC curve will be more useful than just looking at the AUC summary measure. And if you use the ROC together with (estimates of the) costs of false positives and false negatives, along with base rates of what you’re studying, you can get somewhere.

Also note that the AUC only measures discrimination, not calibration. That is, it measures whether you can discriminate between two persons (one ill and one healthy), based on the risk score. For this, it only looks at relative risk values (or ranks, if you will, cf. the Wilcoxon–Mann–Whitney test interpretation), not the absolute ones, which you should be interested in. For example, if you divide each risk estimate from your logistic model by 2, you will get exactly the same AUC (and ROC).

When evaluating a risk model, calibration is also very important. To examine this, you will look at all patients with a risk score of around, e.g., 0.7, and see if approximately 70% of these actually were ill. Do this for each possible risk score (possibly using some sort of smoothing / local regression). Plot the results, and you’ll get a graphical measure of calibration.

If have a model with both good calibration and good discrimination, then you start to have good model. :)


https://www.svds.com/tbt-learning-imbalanced-classes/
What should I do when my data is imbalanced? This has no definite answer for the same reason that the general question Which learning algorithm is best? has no definite answer: it depends on the data.

That said, here is a rough outline of useful approaches. These are listed approximately in order of effort:

Do nothing. Sometimes you get lucky and nothing needs to be done. You can train on the so-called natural (or stratified) distribution and sometimes it works without need for modification.
Balance the training set in some way:
	Oversample the minority class.
	Undersample the majority class.
	Synthesize new minority classes.
Throw away minority examples and switch to an anomaly detection framework.
At the algorithm level, or after it:
	Adjust the class weight (misclassification costs).
	Adjust the decision threshold.
	Modify an existing algorithm to be more sensitive to rare classes.
Construct an entirely new algorithm to perform well on imbalanced data.


Oversampling and undersampling
Bayesian argument of Wallace et al.
Neighbor-based approaches
Synthesizing new examples: SMOTE and descendants
Adjusting class weights
New Algorithms
	In 2014 Goh and Rudin published a paper Box Drawings for Learning with Imbalanced Data5 which introduced two algorithms for learning from data with skewed examples. These algorithms attempt to construct “boxes” (actually axis-parallel hyper-rectangles) around clusters of minority class 