# 14 - Exploring Antifragility and ML Model Environmental Impact


Congratulations on reaching this last chapter - by now you know how to build, test, deploy, detect bias in, and monitor ML models. In this fourteenth chapter, we discuss techniques on how to make your production ML model less vulnerable and to understand the environmental impact of ML models. In this chapter you will be able to:

-   Understand antifragility and how it can be used to make your ML model less vulnerable
-   Determine the environmental impact of training your ML model and how you can help


We start with the concept of antifragility.


##  What is Antifragility?


Antifragility as the name suggests is the opposite of fragility [[1]](Chapter14.html#ftnt1). So what does that mean? Let's start with fragility - it means things or systems that break down when they are subject to randomness, for example with vibrations. So what is the opposite of this phenomenon - well, it is not that things or systems can handle randomness. That is called robustness. The opposite is that instead of breaking down, systems become stronger when subject to randomness. This is called antifragility.


Machines are fragile - they do not do well when encountering randomness. Humans, on the other hand, are antifragile. When we manage unforeseen situations (i.e. randomness) we gain additional experience and become stronger. In the next section, we discuss the association between antifragility and ML models.


##  Chaos Engineering - Antifragility with ML Models


The concept is to use a human (antifragile) and machine (fragile) tandem to make ML models antifragile. This technique is called chaos engineering   [[2]](Chapter14.html#ftnt2)    and it has an open-source toolkit   [[3]](Chapter14.html#ftnt3)   .  It started with Netflix   [[4]](Chapter14.html#ftnt4)    where they improved the resiliency of their systems by developing an experimental methodology called chaos monkeys. They imagined a scenario where monkeys were let loose in their data center and the monkeys would randomly disable production servers. The test was to check if Netflix could survive this scenario without any impact to the customer.


Chaos monkeys expanded to random failures and abnormal conditions across different parts of a production system (for example, production database) and the ability of a system to perform under such duress. Once such conditions were successfully tested, the systems were not only robust but antifragile given that the failures and/or abnormal scenarios exposed fault lines that were plugged to make the system stronger. Chaos monkeys is available on github   [[5]](Chapter14.html#ftnt5)   . In the next section, we go through the principles of chaos engineering to make your ML model production system stronger.


##  Experimentation with Chaos Engineering


The idea is to use controlled experiments to uncover fault lines in the ML model production system. This typically uncovers issues in hidden places such as -

1.   Infrastructure - platform or databases used in production systems.


1.   Applications - bugs in ML model code and outside dependencies such as remote server connections.


1.   Practice or process - bug tracking to ensure that found issues are plugged and tested.


You need to run the experiments with a controlled “blast radius” such that if issues are uncovered their impact is limited to within the radius and they do not bring down the whole (production) system.


Before you run a controlled chaos experiment, you need to establish a hypothesis on the possible failures and prioritize them. One way to prioritize is to label each failure in terms of their likelihood (how often this is likely to happen) and impact (how badly can this failure damage the system operations). Use a grid as in Figure 14.1 to understand where each failure lies.


After you prioritize the failures, run controlled chaos experiments such as -

1.   Infrastructure - randomly bring down servers within a set group such that you understand and control the failure.


1.   Applications - randomly disable outside servers communicating with the ML model - for example, disable a database that is storing ML model parameters.


1.   Process - repeat failures that were discovered earlier and supposed to have been fixed such that the ML model system is robust to them.


<!-- <p align="center">
  <img src="images/images14/image1.png" alt="Alt text" width="50%" />
  <br>
  <em>Figure 14.1: Plot to prioritize system failures in terms of how often they happen (Likelihood) and their influence (Impact)
</em>
</p> -->

<center>

![](images/images14/image1.png)

  Figure 14.1: Plot to prioritize system failures in terms of how often they happen (Likelihood) and their influence (Impact)

</center>


Failures or fault lines exposed by these tests once corrected are going to make your ML model production system stronger using antifragility.


In the next section, we discuss the environmental impact of training and running your ML models.


##  Environmental Impact of ML Models


The last topic in this book is the environmental impact of ML models. While we are all aware of the amazing achievements of ML models, we may not be privy to their environmental impact. Training ML models requires storage and processing power to analyze vast amounts of data and run multiple experiments across days and weeks. This is quantifiable in terms of carbon emissions.


It is recommended that when you are building pipelines and training ML models, you also calculate the carbon emissions. CodeCarbon   [[6]](Chapter14.html#ftnt6)       is a software package that seamlessly integrates into the Python codebase. The solution was jointly developed by Mila, an AI research based in Montreal, GAMMA, BCG’s global data science and AI team, Haverford College in Pennsylvania, and Comet.ml, an MLOps solution provider. It estimates the amount of carbon dioxide ($CO_2$) produced by the computing resources used to execute the code. The objective is to incentivize developers to optimize their code efficiency in terms of carbon emissions.


Additionally, if you are using a cloud provider for ML model training you can use a $CO_2$ calculator   [[7]](Chapter14.html#ftnt7)     to choose the least carbon-emitting data center. One of the factors in the calculation is the power grid used by the cloud provider based on whether the energy is generated using renewable (solar, hydro, wind) or nonrenewable (coal) sources.


Research highlights the environmental price to pay to train large language models [[8]](Chapter14.html#ftnt8).


## Summary


In this last chapter we look at a toolkit based on antifragility that helps improve ML model system resiliency and makes them highly available. We also discussed the environmental impact of training ML models and how to calculate them.

------------------------------


[[1]](Chapter14.html#ftnt_ref1)      Taleb N.N.,   Antifragile: Things That Gain from Disorder (Incerto). Random House Publishing Group, 2014.


[[2]](Chapter14.html#ftnt_ref2)      Miles R.,   Learning Chaos Engineering  . O’Reilly, 2019.


[[3]](Chapter14.html#ftnt_ref3)       [https://chaostoolkit.org/](https://www.google.com/url?q=https://chaostoolkit.org/&sa=D&source=editors&ust=1681619324387510&usg=AOvVaw3JK43EEiiWr6KZsK-gfwyc)


[[4]](Chapter14.html#ftnt_ref4)      [https://netflixtechblog.com/the-netflix-simian-army-16e57fbab116](https://www.google.com/url?q=https://netflixtechblog.com/the-netflix-simian-army-16e57fbab116&sa=D&source=editors&ust=1681619324386160&usg=AOvVaw1B0RCIxVL8TM0cMhybrmsO)


[[5]](Chapter14.html#ftnt_ref5)      [https://github.com/netflix/chaosmonkey](https://www.google.com/url?q=https://github.com/netflix/chaosmonkey&sa=D&source=editors&ust=1681619324386909&usg=AOvVaw354ekilB0mdlw7CU-1oJDM)


[[6]](Chapter14.html#ftnt_ref6)       [https://pypi.org/project/codecarbon/](https://www.google.com/url?q=https://pypi.org/project/codecarbon/&sa=D&source=editors&ust=1681619324388073&usg=AOvVaw1FVa-x-oIbsl_2mw1bvBVL)


[[7]](Chapter14.html#ftnt_ref7)       [Machine Learning CO2 Impact Calculator (mlco2.github.io)](https://www.google.com/url?q=https://mlco2.github.io/impact/%23compute&sa=D&source=editors&ust=1681619324388603&usg=AOvVaw2kv-ehmE9uydA4xddTjww8) (mlco2.github.io)


[[8]](Chapter14.html#ftnt_ref8)  2023 State of AI in 14 Charts, https://hai.stanford.edu/news/2023-state-ai-14-charts.

\newpage
