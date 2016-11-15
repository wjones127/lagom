---
layout: post
title: When Comparing Models, Label Them with Variables
categories:
- blog
---

When analyzing data, we rarely fit just one model.
When assessing the risk of overfitting, we compare the performance of simple and
complex models on out of sample data. When making predictions, we often use an
ensemble of multiple models to get more accurate predictions than we could with
any one model. When we are trying to understand a complex model, we will
compare the inferences from the model with simpler models. Fitting and
comparing multiple models serves many vital purposes in data analysis.

There's one point in the comparing models workflow I'd like to focus on: How do
we keep track of the differences between multiple models? The easiest way to
label models is by number. We call our first model "Model 1", our
next model "Model 2", and so on. But I would like to encourage you to take
labeling models a step further by labeling models with variables. What do I
mean by this? I think this is best illustrated with an example.

Let's look at an example from my undergraduate thesis, [Multilevel Models and
Missing Data Models for Crowdsourced Bicycle Route Ratings](https://github.com/wjones127/thesis).
The goal of the thesis
was to build models that predicted whether cyclists would rate their commutes
negatively, in part based on the time of day and which rider was riding. The
plot below shows the prediction of the probability of negative rating for rides
by their time of day for six different models.

![Plot of model predictions for six different models, labelled "Model 1"
through "Model 6".](/figs/model_vars_before.png)

So each of the models show unique patterns in their predictions by time of day
but, because the models are only labelled by the numbers, you can't tell what it
is about the models that make their predictions different. What's actually going on
is some of these models are controlling for time of day and some are controlling
for rider (and some have both). We can make this clearer by labeling the model
with variables: an indicator variable for rider control and an indicator for
time control. If we write out the table of models and variables, we can see
right away that we have duplicates:

<table>
<thead>
<tr><th>Model #</th><th>Rider control</th><th>Time control</th></tr>
</thead>
<tbody>
<tr><td>1</td><td>False</td><td>False</td></tr>
<tr><td>2</td><td>True</td><td>False</td></tr>
<tr><td>3</td><td>True</td><td>True</td></tr>
<tr><td>4</td><td>True</td><td>True</td></tr>
<tr><td>5</td><td>True</td><td>True</td></tr>
<tr><td>6</td><td>False</td><td>True</td></tr>
</tbody>
</table>

For this part of the analysis, the distinction between models 3-5 wasn't
meaningful. (They just varied in the way the time variable was fit.) So I could
narrow it down to just models 1, 2, 4, and 6, and plot them with labels for
which controls they include:

![Plot of same model prediction, but for only four models arranged in a two by two
grid. The two models on the left are labelled "With rider control" and the models
on the right with "Without rider control." The models on top are labelled "with
time control" and the models on the bottom are labelled "without time control."
](/figs/model_vars_after.png)

That's a lot better! It's clear now how these models differ and why we are
comparing them: how ride ratings vary over time of time has to do not just with
when they are riding, but who is riding.

So next time you are building multiple models, consider how they are different
and label them. Use these labels when comparing them in visualizations, so your
readers can how your models differ. Use these labels when you are doing analysis
to find models you have overlooked. 

This is just one piece of advice in an area many data scientists are grappling with. 
David Robinson and Hadley Wickham, in particular, have made great contributions
in thinking about how we should manage multiple models.
[David Robinson's package broom](https://github.com/dgrtwo/broom) allows you to
convert model output in R into [tidy data frames](http://vita.had.co.nz/papers/tidy-data.pdf).
Hadley Wickham's talk [Managing many models with R](https://www.youtube.com/watch?v=rz3_FDVt9eg)
demonstrates some ways to do this cleanly using nested data frames and concepts
from functional programming. He also writes about managing many models in 
[R for Data Science](http://r4ds.had.co.nz/many-models.html).

