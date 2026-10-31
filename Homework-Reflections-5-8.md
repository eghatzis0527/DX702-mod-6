In all cases, written answers (apart from code) should not be longer than about three paragraphs.  Graders may not read all of your
submission if it is longer than that.

Homework Reflection 5

1. Draw a diagram for the following negative feedback loop:

Sweating causes body temperature to decrease.  High body temperature causes sweating.

A negative feedback loop means that one thing increases another while the second thing decreases the first.

Remember that we are using directed acyclic graphs where two things cannot directly cause each other.

High Body Temp. (at t) --> Begin Sweating --> Sweat Evaporates --> Lower Body Temp. (t+1)

2. Describe an example of a positive feedback loop.  This means that one things increases another while the second things also increases the first.

An example of a positive feedback loop would be studying for an exam and getting a better grade on the test you are studying for. The more time you spend studying a topic, the more you understand it and can then apply this knowledge to the test and get a better grade. Getting the better grade can then motivate you to spend the same amount of time or more studying for the next test in the class.

3. Draw a diagram for the following situation:

Lightning storms frighten away deer and bears, decreasing their population, and cause flowers to grow, increasing their population.
Bears eat deer, decreasing their population.
Deer eat flowers, decreasing their population.

Lightning Storm --> Deer and Bears Run Away --> Deer and Bear populations decrease -->
Flowers begin to grow --> Deer and Bears Return --> Deer and Bear populations increase
                                                        |                       |
                                                        v                       v
                                                Bears Eat Deer              Deer Eat Flowers
                                                        |                       |
                                                        v                       v
                                        Deer Population Decreases     Flower Population Decreases

Write a dataset that simulates this situation.  (Show the code.) Include noise / randomness in all cases.

Code to Build Dataset:
num_days = 100

deer_pop = 200
bear_pop = 50
flower_pop = 750

data = []

for day in range(num_days):

    # Determines if lightning occurs, 8% chance of happening
    lightning_occurs = np.random.binomial(1, 0.08)

    # Number of Deer Leaving
    # Around 3% of deer leave without a storm and around 30% of deer leave with a storm
    deer_leaving = np.random.binomial(deer_pop, 0.3 if lightning_occurs else 0.03)
    deer_pop = deer_pop - deer_leaving

    # Number of Bear Leaving
    # Around 3% of bear leave without a storm and around 20% of bear leave with a storm
    bear_leaving = np.random.binomial(bear_pop, 0.2 if lightning_occurs else 0.03)
    bear_pop = bear_pop - bear_leaving

    # Flower Growth
    # On average 12 flowers grow with a storm, and without 5 flowers grow on average 
    # with a standard deviation of 3 flowers

    flower_growth = max(0, int(np.random.normal(12 if lightning_occurs else 5, 3)))
    flower_pop += flower_growth

    # Number of Animals that Return
    # Around 40% of animals that leave due to a storm return, around 10% of animals that left without storm return

    deer_returning = np.random.binomial(deer_leaving, 0.4 if lightning_occurs else 0.1)
    bear_returning = np.random.binomial(bear_leaving, 0.4 if lightning_occurs else 0.1)

    deer_pop += deer_returning
    bear_pop += bear_returning

    # Number of Deer Eaten
    # Each bear eats around 20% of a deer per day

    deer_eaten = min(deer_pop, np.random.poisson(0.2 * bear_pop))
    deer_pop = deer_pop - deer_eaten

    # Number of Flowers Eaten
    # Each deer eats around 50% of a flower per day

    flowers_eaten = min(flower_pop, np.random.poisson(0.5 * deer_pop))
    flower_pop = flower_pop - flowers_eaten

    data.append({'Day' : day + 1, 'Lightning' : lightning_occurs, 'Deer Population' : deer_pop,
    'Bear Population' : bear_pop, 'Flower Population' : flower_pop, 'Deer Leaving' : deer_leaving,
    'Bears Leaving' : bear_leaving, 'Flower Growth' : flower_growth, 'Deer Returning' : deer_returning,
    'Bears Returning' : bear_returning, 'Deer Eaten' : deer_eaten, 'Flowers Eaten' : flowers_eaten})


data_df = pd.DataFrame(data)

Identify a backdoor path with one or more confounders for the relationship between deer and flowers.

A potential confounder that could lead to a backdoor path for the relationship between deer and flowers would be the lightning storm. This variable can effect both the number of deer in the area as well as the number of flowers that are in the area. If the storm occurs, deer will get scared away, lowering the population and leading to less flowers getting eaten. However, the storm also leads to an increase in the flower growth so the flower population also increases due to the storm. This confounder could then lead to a misinterpretation on how much of the change in flowers is caused by the deer.

4. Draw a diagram for a situation of your own invention.  The diagram should include at least four nodes, one confounder, and one collider.  Be sure that it is acyclic (no loops).  Which node would say is most like a treatment (X)?  Which is most like an outcome (Y)?

The situation I am diagraming is sewing a garment designed by yourself. The confounder in this situation would be your sewing experience as it can affect how well you sew the garment which would affect the quality, and then it will also affect how long it takes you to sew the garment. It will affect your time sewing because if you are a more experience sewer, you are able to sew pieces together with fewer stops and sew faster than a beginner. The collider in this scenario would be if the garment is completed on time, which is caused by the complexity of your design as well as how fast you sew the garment together. If the design is more complex, it will take more time and make it less likely to be completed on time. Also, if you sew at a slower pace, you will take longer to complete the garment and therefore may not complete it on time. The diagram is below. Another colider could be the garment quality as well, which is affected by both the design complexity and the time spent sewing. If you spend little time and rush sewing the piece together, it would lead to the garment being of lower quality. Furthermore, the complexity of the design could also lead to the garment being of higher quality. You could include more complex finishing techniques for edges of the garment to make it higher quality, or could include simpler or no finishing techniques for the edges making it poorer quality. 

                                          Sewing Experience
                                        /                  \
                                       v                    v
           Design Complexity ---> Garment Quality <--- Time Spent Sewing
                |                                               |
                |                                               |
                ---------------------> Completed on Time <-------

Homework Reflection 6

1. What is a potential problem with computing the Marginal Treatment Effect simply by comparing each untreated item to its counterfactual and taking the maximum difference?  (Hint: think of statistics here.  Consider that only the most extreme item ends up being used to estimate the MTE.  That's not necessarily a bad thing; the MTE is supposed to come from the untreated item that will produce the maximum effect.  But there is nevertheless a problem.)
Possible answer: We are likely to find the item with the most extreme difference, which may be high simply due to randomness.
(Please explain / justify this answer, or give a different one if you can think of one.)

Selecting the MTE based off of the the most extreme difference, we may find the MTE is unusually high due to random variation within the data or caused by outliers. This can lead to us overestimating the MTE, and in turn reporting a statistic that is not accurate to the entire dataset and misinterpreting our findings.

2. Propose a solution that remedies this problem and write some code that implements your solution.  It's very important here that you clearly explain what your solution will do.
Possible answer: maybe we could take the 90th percentile of the treatment effect and use it as a proxy for the Marginal Treatment Effect.
(Either code this answer or choose a different one.)

I agree with the possible answer that a solution could be to take the 90th percentile of the treatment effect and get an estimate of the MTE. By taking the 90th percentile and not using the maximum MTE allows us to reduce the influence of the extreme values, which could be caused by random variation in the data. This would provide us a more reliable estimate of the upper range of treatment effects for the data without allowing extreme values determine the result.

 In the code below, I created a randomized set of data where the X is a mix of 0s and 1s, Z is the random variation for Y, and then Y is constructed of Y0 and Y1 depending on the treated. If someone is treated, they will have an added effect of 4 + Z (leading to 9 + 2 * Z) to make the Y values differ. I added another instance of the Z value to ensure that there was a guaranteeded random variation in the treated y values. I then found the treatment effect for each instance, and then took the 90th percentile to get the proxy marginal treatment effect.

Code:

X = np.random.binomial(1, 0.5, 1000)

Z = np.random.normal(0, 1, 1000)

Y0 = 5 + Z
Y1 = 9 + 2 * Z

Y = np.where(X == 1, Y1, Y0)

data = pd.DataFrame({'X' : X, 'Y' : Y, 'Y0' : Y0, 'Y1' : Y1})

data['treatment_effect'] = data['Y1'] - data['Y0']

mte = np.percentile(data['treatment_effect'], 90)

Homework Reflection 7

1. Create a linear regression model involving a confounder that is left out of the model.  Show whether the true correlation between $$X$$ and $$Y$$ is overestimated, underestimated, or neither.  Explain in words why this is the case for the given coefficients you have chosen.

Code:

coefs = []

for i in range(1000):

    W = np.random.normal(0, 1, 100)
    X = 4 * W + np.random.normal(0, 1,100)
    y = 3 * X + 5 * W + np.random.normal(0, 1, 100)

    X = pd.DataFrame({'X' : X})
    X = sm.add_constant(X)

    model = sm.OLS(y, X).fit()

    coefs.append(model.params['X'])

avg_coef = np.mean(coefs)

print(f'Estimated Average Coeffficient for X without W: {avg_coef}')

The estimated average coefficient for X without the confounder W came out to 4.18 when the true coefficient was 3. I did construct an average since everytime the model is run, the coefficient will have some variation due to the randomness of X, W and y. The estimated coefficient was overestimated, with the estimated coefficient having a difference of 1.18 from the actual coefficient. This is caused by the model attributing some of the positive effect that W actually provides in the construction of y to X since the model is not provided W to predict the y values. I do want to note that the coefficient may change slightly due to randomness

2. Perform a linear regression analysis in which one of the coefficients is zero, e.g.

W = [noise]
X = [noise]
Y = 2 * X + [noise]

And compute the p-value of a coefficient - in this case, the coefficient of W.  
(This is the likelihood that the estimated coefficient would be as high or low as it is, given that the actual coefficient is zero.)
If the p-value is less than 0.05, this ordinarily means that we judge the coefficient to be nonzero (incorrectly, in this case.)
Run the analysis 1000 times and report the best (smallest) p-value.  
If the p-value is less than 0.05, does this mean the coefficient actually is nonzero?  What is the problem with repeating the analysis?

Code:
smallest_p_value = 1

for i in range(1000): 
    X = np.random.normal(0, 1, 100)
    W = np.random.normal(0, 1, 100)
    Y = 2 * X + np.random.normal(0, 1, 100)

    X = pd.DataFrame({'X' : X, 'W' : W})
    X = sm.add_constant(X)
    y = Y

    model = sm.OLS(y, X).fit()

    p_value = model.pvalues['W']

    if p_value < smallest_p_value:
        smallest_p_value = p_value

print(f'The Smallest P-Value is {smallest_p_value:.4f}')

The p-value came out to 0.0006, making it less than 0.05, which does suggest that the coefficient is non-zero which is not true. This could be caused by a false positive when completing the regression analysis due to the random variation within the W feature. This can especially be caused by repeating the analysis numerous times. By repeating the analysis 1000 times and selecting the smallest p-value, we are increasing our chances to find at least one false positives in the repeats. 


Homework Reflection 8

Include the code you used to solve Week 8's coding quiz problems and write about the obstacles / challenges / insights you encountered while solving them.

Code:

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from sklearn.linear_model import LogisticRegression

data1 = pd.read_csv('homework_8.1.csv')

X = data1[['Z']]

y = data1['X']

model_lr = LogisticRegression()

model_lr.fit(X, y)

data1['propensity_score'] = model_lr.predict_proba(X)[:, 1]

data1.head()

weighted_y_treated = []
weighted_y_untreated = []

for i in range(len(data1)):
    if data1.loc[i, 'X'] == 1:
        ipw = 1/data1.loc[i, 'propensity_score']
        weighted_y_treated.append(ipw * data1.loc[i, 'Y'])
    else: 
        ipw = 1/(1 - data1.loc[i, 'propensity_score'])
        weighted_y_untreated.append(ipw * data1.loc[i, 'Y'])

ATE = (sum(weighted_y_treated) - sum(weighted_y_untreated))/len(data1)

print(f'The average treatment effect is {ATE:.1f}')

data2 = pd.read_csv('homework_8.2.csv')

data2.head()

import scipy

Z_matrix = np.array(data2[['Z1', 'Z2']]).T

cov_matrix = np.cov(Z_matrix)
inv_cov = np.linalg.inv(cov_matrix)

print('Inverted Covariance Matrix')
print(inv_cov)

untreated_rows = data2[data2['X'] == 0]
treated_rows = data2[data2['X'] == 1]

matches = []

for index, treated in treated_rows.iterrows():

    distances = []

    for idx, untreated in untreated_rows.iterrows():
        distance = scipy.spatial.distance.mahalanobis(treated[['Z1', 'Z2']].to_numpy(dtype = float), untreated[['Z1', 'Z2']].to_numpy(dtype = float),
                                                      inv_cov)
        distances.append(distance)

    nearest_index = untreated_rows.index[np.argmin(distances)]

    matches.append((index, nearest_index))

treatment_effects = []

for treated_idx, untreated_idx in matches: 

    treated_y = data2.loc[treated_idx, 'Y']
    untreated_y = data2.loc[untreated_idx, 'Y']

    treatment_effect = treated_y - untreated_y

    treatment_effects.append(treatment_effect)

ATE = np.mean(treatment_effects)

print(f'ATE = {ATE:.4f}')

furthest_distance = 0

for index, treated in treated_rows.iterrows():

    distances = []

    for idx, untreated in untreated_rows.iterrows():
        distance = scipy.spatial.distance.mahalanobis(treated[['Z1', 'Z2']].to_numpy(dtype = float), untreated[['Z1', 'Z2']].to_numpy(dtype = float),
                                                      inv_cov)
        distances.append(distance)

    min_distance = min(distances)

    if min_distance > furthest_distance:

        furthest_index_treated = index
        furthest_index_untreated = untreated_rows.index[np.argmin(distances)]
        furthest_distance = min_distance

print((furthest_index_treated, furthest_index_untreated))

data2.loc[furthest_index_treated, ['Z1', 'Z2']]

data2.loc[furthest_index_untreated, ['Z1', 'Z2']]

For week 8, the biggest obstacle I did end up facing was the wording of Question 4 on the assessment as I was spending a lot of time questioning where my code was messing up since the answer I was getting was incorrect. As other classmates have stated, the wording of the question made it seem like we were looking for the Z1 and Z2 values for the treated instance in the matched pair, which is why I have that included at the end of my code first. However, in Piazza, it was made clear that the answer is actually we are supposed to find the furthest distance and then get the Z1 and Z2 values for the untreated instance of the matched pair, which is what the last code statement is doing. 

As for insights I gained, I gained a better understanding of how the Mahalanobis distance truly works at matching values when there are multiple Z values that can be used. When beginning this weeks homework, I spent some time looking at the documentation for the scipy function and also looking up how this distance works as I was unsure what to provide the function besides the inverse matrix that was stated in the assessments description. By doing this research, I have a better understanding that Mahalanobis distance will compare the two points while also looking at their variance and covariance. This is especially helpful when working with multiple Z values that are somewhat correlated, as it provides a more informed distance of how far apart the two observations are. 