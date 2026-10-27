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

I agree with the possible answer that a solution could be to cap the data at the 90th percentile and get an estimate of the MTE. The capped dataset could be used as a proxy dataset for the original and we can use it to get the estimated MTE. This will help reduce the influence of the high data points that could be within the dataset, and could in turn provide a more robust estimate for the MTE. 

Code:


Homework Reflection 7

1. Create a linear regression model involving a confounder that is left out of the model.  Show whether the true correlation between $$X$$ and $$Y$$ is overestimated, underestimated, or neither.  Explain in words why this is the case for the given coefficients you have chosen.

Code:

W = np.random.normal(0, 1, 100)
X = 4 * W + np.random.normal(0, 1, 100)
y = 3 * X + 5 * W + np.random.normal(0, 1, 100)

X = pd.DataFrame({'X' : X, 'W' : W})
X = sm.add_constant(X)

model = sm.OLS(y, X).fit()

print(f'Estimated Coeffficient for X without W: {model.params['X']}')

The estimated coefficient for X without the confounder W came out to 3.11 when the true coefficient was 3. The estimated coefficient was slightly overestimated, but it was not a very high overestimation with the estimated being only 0.11 higher than the true coefficient. The reason the model is overestimating it slightly is due to the model attributing some of the postive effect from W onto X when it is predicting Y since it does not have W as a feature to attribute the effect to.

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
