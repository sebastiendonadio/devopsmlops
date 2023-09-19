# 8 - Understanding Machine Learning Pipelines


This eighth chapter pulls together data from Chapter 5 (including feature store from Chapter 6) and ML algorithms and model building from Chapter 7 and introduces experimentation using machine learning (ML) pipelines. By the end of this chapter, you will be able to:

-   Understand why ML pipelines are important
-   Understand what to ensure when productionizing pipelines
-   How to create data feedback loops
-   Be cognizant of available open-source ML pipeline tools


We start this chapter with the notion of experimentation that is at the heart of iterative ML model development.


##  Phases of Experimentation


The iterative nature of ML model building was outlined in the last chapter where you managed data versions for different experiments. Experimentation has 3 different phases that form an experimental wheel as defined by Stefan Thomke   [[1]](Chapter8.html#ftnt1):

1.   Generate verifiable hypothesis   - this is where you have a hypothesis that using data version 1.1.1 is better than version 4.0 (Figure 5.2) because including outliers makes the model robust.


1.   Run disciplined experiments   - this is where you have a platform to run experiments where you build a ML model using different data versions (Figure 5.2) and different algorithm and hyperparameter versions (Figure 7.1 and Figure 7.2) and compare the metrics.


1.   Learn Meaningful Insights   - use the feedback from the metrics comparison to learn and derive insights such as imputation is not important but outlier removal is and use these insights to generate the next verifiable hypotheses (for example to use version 2.1.1 in Figure 5.2).


The ability to run the above experimentation wheel is enabled by ML pipelines. And the knobs to change for a ML experiment span data versioning, hyperparameters versioning, and algorithms/pipeline versioning   [[2]](Chapter8.html#ftnt2) (Figure 8.1). For example, you may want to experiment with the same data version in the same ML pipeline but with different hyperparameters. Another experiment would be using the same data version with the same algorithm hyperparameter but different pipeline configurations such as different data mappings or mash-ups with external data. A third configuration may use the same data version with different algorithms. As outlined in Chapter 4 on the desiderata of an MLOps platform, team collaboration with different members running different experiments (some of which fail fantastically like M2 and not all successful ones perform like M1) is important to find that winning model combination.


<!-- <p align="center">
  <img src="images/images8/image2.png" alt="Alt text" width="90%" />
  <br>
  <em>Figure 8.1 - ML experiments
</em>
</p> -->

<center>

![](images/images8/image2.png)

  Figure 8.1 - ML experiments

</center>



##  ML Pipeline


A ML pipeline is a workflow to run experiments outlined in the previous section starting with data ingestion and ending with model delivery.   It includes all the steps necessary to train a model from DataOps and ModelOps in Figure 4.2 such as data validation, data preprocessing, data mappings (including any external data mash-ups), model training, model evaluation, and model delivery (ready for production deployment). Note that this includes defining a ML pipeline for AutoML discussed in Chapter 7. A workflow example is given in Figure 8.2 -

1.   Use a hypothesis to connect to a specific data version.


1.   [Optional] Perform additional mapping/mash-ups on the data.


1.   [Optional] Generate features - each new feature corresponds to a new pipeline version.


1.   Choose an algorithm and specific hyperparameters. Note that changing hyperparameters corresponds to a new hyperparameter version.


1.   Train a ML model (can be manual or Semi-Auto ML or AutoML from Chapter 7).


1.   Calculate model metrics (training error and out-of-sample validation error).


1.   Deliver the trained model ready to be deployed if needed (to be determined based on the model metrics) - this completes the experiment.




<!-- <p align="center">
  <img src="images/images8/image3.png" alt="Alt text" width="90%" />
  <br>
  <em>Figure 8.2: ML pipeline example
</em>
</p> -->

<center>

![](images/images8/image3.png)

  Figure 8.2: ML pipeline example

</center>


Pipelines need orchestration so that the components are executed in the correct order. In the Hidden Technical Debt in Machine Learning paper, the authors found that one of the reasons for the failure of ML projects is the brittle and often not-portable scripts that glue the different components of a ML workflow. This is mitigated by ML Pipelines that abstract the glue code and manage the orchestration in a standardized way.

Pipeline tools manage the flow of tasks through a directed graph representation of the task dependencies (Figure 8.3). Directed graphs ensure that task dependencies are honored. They are acyclic (no cycles) so that the orchestration is not going back to a previously completed task, and are directed such that the execution flows in one direction.



<!-- <p align="center">
  <img src="images/images8/image1.png" alt="Alt text" width="60%" />
  <br>
  <em>Figure 8.3: Directed Acyclic Graph (DAG) used in ML pipelines
</em>
</p> -->

<center>

![](images/images8/image1.png)

  Figure 8.3: Directed Acyclic Graph (DAG) used in ML pipelines

</center>


###  ML Pipeline   Advantages


The technical advantages of ML pipelines are -

1.   Independent   steps   so multiple team members can collaborate on the pipeline simultaneously.


1.   Start   from any specific point -   Re-run   does not have to always start at the beginning, you can choose a midpoint skipping steps not needed.


1.   Reusability   promoted by pipeline templates for specific scenarios that always follow the same sequence - for example, you can create a copy of a pipeline and reuse for another experiment (i.e. use another data version and/or another set of hyperparameters and/or another data mapping/external data mash-up).


1.   Tracking   and   versioning     of data, model and metrics.


1.   Modular development   with isolated changes to independent components that enable faster development time.


1.   Visual representation   of components / functionalities deployed to production.


The primary business advantage provided by ML pipelines is cost reduction due to -    

1.   Multiple quick iterations   via different experiments that give more development time for novel models – something that data scientists love to do = better job satisfaction + retention.


1.   Standard processes   to update existing models.


1.   Systematic   methodology to reproduce results.


1.   Increased data governance   with data and model audit – create a paper trail.


###  Inference Pipelines


So far in this chapter we have discussed training ML pipelines where you run different experiments to create the best (from your metrics-perspective) ML model and deliver it ready for deployment. That model is then deployed to production and is ready for inferencing. Here you have to be diligent so that your inference pipeline includes all the data mapping(s) and mash-up(s) and feature engineering that were done on the raw incoming data in the training pipeline. As illustrated in Figure 8.4, all the steps prior to model run have to be identical so that there are no differences between the data used to train the algorithm to generate the deployed model, and the data used by the same model for inferencing.


If there is a difference in the data sent to the model then your pipelines have a Training-Serving skew. Be careful to avoid this skew. Otherwise you violate one of the basic assumptions in machine learning that the training and inference data are from the same data distribution.


Note that training and inference pipelines are complementary. The training pipeline's primary purpose is to evaluate models and deliver the one with the best metrics. The inference pipeline’s primary purpose is to generate results from the model and monitor the model for any drift (more on this in Chapter 12). More on this result generation in the next section.


<!-- <p align="center">
  <img src="images/images8/image5.png" alt="Alt text" width="90%" />
  <br>
  <em>Figure 8.4: Complementary Train and Inference ML Pipelines
</em>
</p> -->

<center>

![](images/images8/image5.png)

  Figure 8.4: Complementary Train and Inference ML Pipelines

</center>



###  Data Feedback and Flywheel


As you can see in the Inference pipeline above, there is a “store results + data” component. This component constitutes data feedback and stores new input data to the model during inference. The stored data serves as new training data that is used in the training pipeline.


There are 2 types of data feedback in ML models -

1.   Implicit feedback   - a user clicks on an online advertisement implying the ad was useful.


1.   Explicit feedback   - a user rates a movie in an online streaming service.


The motivation behind data feedback is to institute a cycle of model retraining and data collection also known as data flywheel (Figure 8.5). The flywheel concept is from Jim Collins   [[3]](Chapter8.html#ftnt3) and it depicts a cycle that is enabled by all the different components and leads to (continuous) improvement.


<!-- <p align="center">
  <img src="images/images8/image6.png" alt="Alt text" width="60%" />
  <br>
  <em>Figure 8.5: Data Flywheel
</em>
</p> -->

<center>

![](images/images8/image6.png)

  Figure 8.5: Data Flywheel

</center>

From a ML perspective, the data flywheel concept is tied to Continuous Training (CT). CT forms the third part of a CI/CD/CT pipeline where a ML model is retrained with new training data. The need to retrain is enabled by different triggers such as data drift (Chapter 12) or a specific time heuristic such as the first of each month. The data flywheel ensures that when CT is triggered there is new training data available to deliver an updated model. For example, a recommender system is a good example of collecting training data using a feedback loop - when a user clicks on a recommendation that is used as positive (implicit) feedback. Let us take a closer look at CT.

##  Continuous Training (CT)
In ML, CT extends the DevOps CI/CD to CI/CD/CT. Continous training is triggered with new data and is executed in either methodology

1. Stateless training - this is when training with the new data deletes all current model (parameter) weights (erases all model knowledge) and starts training the model from scratch. This works well when you do not have a lot of training data to restrict the time (and cost) to regularly train a model from scratch.


1. Stateful training (aka fine-tuning or transfer learning) - this is when training with the new (additional) data builds upon the knowledge (parameters) of the current model (keeping the existing parameter values). This works well when you have a large dataset that keeps growing such that training from scratch is expensive (time and cost).

In the next section, we outline three popular open-source pipelines to use with your ML code.


###  Open Source Pipeline Implementations


There are multiple open-source pipeline implementations and we list 3 of them in this chapter. Note that most of these implementations use YAML (Yet Another Markup Language) files to define the pipeline components and dependencies (including external library versions). An example of such a YAML file pipeline definition is in Figure 8.6.


```yaml
name: spacy-example
channels:
 - conda-forge
depencies:
 - python=3.7
 - pip
 - pip:
   - mlflow >= 1.0
   - spacy==2.2.3
```
<p align="center">

  Figure 8.6: YAML file from spacy example of MLflow.
</p>

The popular pipeline frameworks are -

1.   Apache Airflow (   [https://airflow.apache.org](https://www.google.com/url?q=https://airflow.apache.org/&sa=D&source=editors&ust=1681619251622052&usg=AOvVaw39NOsSCkt-Z01yjDPApPgX)    /) - originated at Airbnb this is a workflow engine that manages scheduling and running jobs and data pipelines.


1.   Kubeflow Pipelines (   [https://www.kubeflow.org/](https://www.google.com/url?q=https://www.kubeflow.org/&sa=D&source=editors&ust=1681619251622572&usg=AOvVaw35Pnm3-5cRZChF5k2KT4rR)    ) - started as an open-source Google to manage TensorFlow jobs on Kubernetes.


1.   MLflow (   [https://mlflow.org/](https://www.google.com/url?q=https://mlflow.org/&sa=D&source=editors&ust=1681619251622891&usg=AOvVaw2iy_Rm7bNR0BuXhuY99Jj2)    ) - provides a framework to track experiment metrics and  parameters and visualize and compare them in a browser.



##  Fine-tuning Large Language Models (LLMops)

Large Language Models (LLMs) have billions of parameters that constrain the amount of RAM memory left for the data. For example, a 7B LLM assuming a 32-bit (4 Bytes) architecture will require at least 7 * 4 = 28 GB of RAM. Parameter Efficient Training (https://github.com/huggingface/peft) from HuggingFace introduces techniques to fine such large models in an efficient manner. One of the popular techniques is Low-Rank Adaption (LoRA) [[4]](Chapter8.html#ftnt4). In this technique, a pre-trained LLM is fine-tuned with transfer learning where the weight updates are managed in a lower dimension than the original (large) dimension. The weight update matrix is decomposed to a lower dimension (much smaller than the dimension of the large mnodel weight matrix) using Singular Value Decomposition (SVD). The original weight matrix that has a large dimension is kept frozen while the lower dimension weight-change matrix is updated with the new data.

Research has demonstrated that the lower dimension matrix performs comparatively well when the dimension is very small compared to relatively higher dimension. This is because with SVD the significant features (top vectors) that account for majority of the weight changes are common in both the very small and relatively higher dimensions. Moreover, LoRA is effective since the change matrix amplifies the important features that are not given high weight in the original weight matrix. You can use a HuggingFace guide to try LoRA [[5]](Chapter8.html#ftnt5).

##  Summary


In this chapter we looked at the motivations behind ML pipelines and the advantages of using them both from technical and business perspectives. We also outlined continuous training that extends the DevOps CI/CD methodology to CI/CD/CT using a data flywheel. Lastly, we present 3 popular open source implementations for ML pipelines. In the next chapter, we look at model interpretability and explainability.

------------------------------


[[1]](Chapter8.html#ftnt_ref1)     Thomke Stefan H., Experimentation Works, Harvard Business Review, 2020


[[2]](Chapter8.html#ftnt_ref2)      [How MLOps Helps CIOs Reduce Operations Risk (xpresso.ai)](https://www.google.com/url?q=https://xpresso.ai/resources/blogs/how-mlops-helps-cios-reduceoperations-risk/&sa=D&source=editors&ust=1681619251623850&usg=AOvVaw3ok7vUwIesXkZNVAxHxJ0A) (xpresso.ai)


[[3]](Chapter8.html#ftnt_ref3)    J. Collins,    [Good to Great: Why Some Companies Make the Leap and Others Don’t, HarperBusiness, 2001.](https://www.google.com/url?q=https://smile.amazon.com/Good-Great-Some-Companies-Others/dp/0066620996/ref%3Dsr_1_1?keywords%3Djim%2Bcollins%2Bgood%2Bto%2Bgreat%26qid%3D1662917929%26sprefix%3DJim%2Bcol%252Caps%252C103%26sr%3D8-1&sa=D&source=editors&ust=1681619251624332&usg=AOvVaw2Nw2smFXf9pQ0pnNupy-8E)


[[4]](Chapter8.html#ftnt_ref4)   E. Hu et al, _LoRA: Low-Rank Adaptation of Large Language Models_, https://arxiv.org/pdf/2106.09685.pdf, 2021.


[[5]](Chapter8.html#ftnt_ref5)   https://huggingface.co/docs/peft/task_guides/image_classification_lora 

\newpage
