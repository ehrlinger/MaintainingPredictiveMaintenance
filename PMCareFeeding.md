---
title: "Care and feeding of machine learning solutions."
subtitle: "Predictive maintenance with Azure Machine Learning"
author:
  - name: John Ehrlinger
    affiliation: Microsoft
    email: john.ehrling@microsoft.com
date: "January 11, 2018"
output:
  rmdformats::readthedown:
    highlight: kate
    keep_md: true
    fig_caption: yes
  html_document:
    fig_caption: yes
    toc: true
    toc_float: true
    code_folding: hide
  word_document:
    fig_caption: yes
    toc: true
fontsize: 10pt
---
Microsoft has recently launched [Azure Machine Learning services](https://docs.microsoft.com/en-us/azure/machine-learning/preview/overview-what-is-azure-ml) (AML) to public preview. The updated services include a Workbench application plus command-line tools to assist in developing and managing machine learning solutions through the entire data science life cycle. An Experimentation Service handles the execution of machine learning experiments and provides project management, Git integration, access control, roaming, and sharing of work. The Model Management Service allows data scientists and dev-ops teams to deploy predictive models into a wide variety of environments. Model versions and lineage are tracked from training runs to deployments while being stored, registered, and managed in the cloud. 

Once [AML Workbench is installed](https://docs.microsoft.com/en-us/azure/machine-learning/preview/quickstart-installation), the app connects to a Gallery of prebuilt real world data science scenario projects to help new users explore Azure Machine Learning, as well as give users a jump start on their specific data science scenarios. 

The AML gallery currently contains two predictive maintenance example scenarios:

 * A PySpark implementation using a random forest of decision trees classifiers: 
 
 https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance
 
 * A deep learning approach using an LSTM classifier:
 
 https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance

This article is written specifically to prepare users interested in using their own data in deploying a customized predictive maintenance model. We will discuss the prerequisites to building and requirements for maintaining an intelligent, machine learning predictive maintenance solution. We will define what predictive maintenance means and discuss data requirements for creating a solution. We also discuss the end result of the product and how to maintain your operationalized machine learning solution. 

# Equipment maintenance

To understand predictive maintenance, we first contrast it with other maintenance strategies. There are three general classes of equipment maintenance strategies, each with a specific set of benefits and drawbacks. 

## 1. Reactive maintenance

Reactive maintenance is a _replace after failure_ strategy. This strategy ensures that each component has the maximum possible service life, but could cause down time or safety issues when the component does fail.

The following figure details the life of a set of 50 devices (stacked along the y-axis) and the service life time is aligned along the x-axis. Each device fails at some point in time denoted by the red "x", ending the devices service life. It's obvious that this strategy maximizes device life (utilization), but at the cost of failures at unknown time.

![Reactive Maintenance strategy](PMCareFeeding_files/figure-html/react-1.png)

A reactive maintenance strategy is acceptable when there is little safety risk, or if the device failure is not catastrophic, causing more expensive damage. This is typically the strategy we use for home items and even some auto parts.

In industrial scale reactive maintenance settings, it may be valuable to the business to add a machine learning solution that notifies the business when a failure may have occurred. A common approach would be to use anomaly detection, an unsupervised method designed to detect when a device is behaving differently than expected. These different behaviors may be related to the failure type of interest. An Anomaly detection solutions can be more accurate than simple rule base failure detection methods, and is useful when notification could be used to prevent more expensive failures or outages.

## 2.  Preventative maintenance

Preventative maintenance replaces components on a specific schedule, regardless of remaining useful service life. This strategy minimizes downtime and maximizes safety by setting the replacement schedule to minimize component failures. Preventative maintenance can additionally control some maintenance costs, since the maintenance schedule and costs are known a priori.

Using the same devices in the previous figure, imagine we instituted a preventative maintenance strategy of replacing devices at a service life time of 10 (indicated by the vertical dashed line). The following figure shows how this strategy will capture and prevent a large number of failures. However, many of these devices could have been in service much longer, and it is still possible to miss some failures.

![Preventative Maintenance strategy](PMCareFeeding_files/figure-html/unnamed-chunk-1-1.png)

The preventative maintenance schedule time may be chosen by determining how many failures are tolerable for the business, either using cost, customer safety or some combination of related factors. In our example case, we allow about 10% of the devices to fail. We could shorten the acceptable service life if we wanted to ensure no failures, or lengthen it if we were tolerant of more failures. 

## 3. Predictive maintenance

The goal of the predictive maintenance strategy is to both maximize the useful service life of equipment and prevent failures at the same time. Instead of replacing components on a fixed service life schedule, a perfect preventative maintenance schedule would move the maintenance schedule to a "just--in--time" approach.

The following figure shows how a perfect preventative maintenance strategy could replace any device directly before a failure, maximizing component life, and minimizing failure events.

![Predictive Maintenance strategy](PMCareFeeding_files/figure-html/unnamed-chunk-2-1.png)

Where reactive and preventative maintenance strategies require some initial thought, once the strategy is in place, maintaining the strategy requires little effort. Predictive maintenance strategies, in contrast have assumptions and consequences during and after implementation. The remainder of this article will address some of these concerns.

# Predictive maintenance requirements

A predictive maintenance machine learning strategy is a _supervised learning_ process, meaning we have data detailing the full life history of a series of devices. We use this data to characterize how other devices, unseen but assumed to be identical, would behave over their service life.

In order to ensure you have the complete life history of your set of training devices, a supervised machine learning method will require at least the following: 

**Component life history** The model needs to know when each component was placed into service (this is $t_0$), and any periods when it was removed to properly account for service life hours.

**Component Maintenance history** Some times a failure is only noted during a maintenance visit, which complicates determining exactly when the failure occurred. Maintenance records may also be useful by knowing when a technician inspected the device. We can use this information to infer the device was actually working correctly. This information can be then be used to improve the accuracy of the predictive maintenance model. 

**Event history** In order to predict failures, the data must contain examples of those failures. A rule of thumb is that your data should contain on the order of 10-30 events per feature you'd like to resolve. This means that your data should contain at least 300 examples of the failure types you are interested in predicting in order to build a reasonable machine learning solution. More examples will result in better, more generalizable predictive maintenance solutions.

In general, you will have a mix of devices that have failed, and those that are still in service. Or you may have devices that have failed for the specific reason you are interested in, collected with devices that have failed for other reasons. In both of these cases, using all the available devices both having and not having failures of interest will help the machine learning solution discern between the different device behaviors.

# Predictive maintenance deployment

The examples in the AML gallery detail the building of a predictive maintenance solution. Microsoft has also published many documents targeted to other platforms:

  * [Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/preview/) - using PySpark [Predictive maintenance real-world scenario]( https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance) 
  * [Predictive Maintenance modeling guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) using R in the [Azure ML studio](https://studio.azureml.net/)

These documents detail the details of feature engineering, machine learning experimentation, and deployment of a predictive maintenance web service. We refer you to these and other solution approaches referenced therein for the approach that most closely matches your requirements. 

Once you have deployed your web service, what will you get and how can the solution guide business decisions?

The predictive maintenance machine learning solutions are time series (sequential predictions at regular intervals) classification (failure/healthy) models. Classification models can return either the resulting binary answer (fail/healthy), or a probability that the device will fail. The time series approach specifies the failure may occur within a specified window of time. 

With the binary return value (fail/healthy), the model can return a set of devices that should be maintained within the time window. With the probability, the model can return a ranked list of devices at risk of failure, where the order indicates devices most probable imminent failures. The decision between these result choices should be driven by the underlying business strategy.

The deployment idea is to periodically run the full population of devices currently in service through the machine learning web service to score each device. The scored results can be used to either deploy maintenance events, or guide the _human in the loop_, a maintenance subject matter expert, to make more inform choices on where to deploy limited maintenance resources.
	
# Predictive maintenance maintenance

A frequent request centers on retraining the predictive maintenance model. When using standard time series models, the approach is to retrain continually, using the current set of observed data to predict forward in time. In this way, the model is continually being updated to capture changes in behavior not seen in training the original model.

In predictive maintenance settings, retraining our model becomes difficult. To see why, remember that the goal of the predictive maintenance solution is to remove components before they fail. The issue becomes clear by examining the predictive maintenance figure above. If we created a perfect model, the solution would remove **all** failure events from the population. Since having the failure events is critical to training the supervised method, retraining a predictive maintenance solution would actually degrade the model, since there is now no new information from which the model can learn. 

Also note that by removing the failure events, we can not infer when the device would have truly failed. This is also the main problem when attempting to transition away from a preventative maintenance strategy. The preventive maintenance figure indicates we have removed the ability to know when the device will actually fail. Because of this, once a predictive or preventive maintenance strategy has been deployed, changes to the system must be carefully weighed. As your business moves from away reactive maintenance, new restrictions on changes possible in the future are imposed. 

  1. Removing either an implemented predictive or preventive strategy will certainly lead to an increase in the number of failures.
  
  2. Because predictive and preventive maintenance are designed specifically to change the predicted quantity, any data collected during this implementation must be interpreted with that information in mind. This is similar to knowing the maintenance history when building the solution to begin with. 

  3. The solution implementation makes retraining (under a strict definition) impossible, because we would need to know that the failures we prevented were true failures and this information is no longer available.
  
This does not mean that we cannot improve on the solutions once implemented. One option would be to build and deploy an initial predictive maintenance solution, and continue to collect failure data. We can not hope to deploy a perfect solution that captures all failures. Instead, we assume that the model has captured the most likely failure modes, and by collecting more data, we can add an additional model to capture the next level of failures. Each subsequent model would be additive in nature, and ranking of failures can be combined to further reduce the failure rate. 

An alternative strategy would be to deploy the model on one population of devices, and continue to collect data an a separate population. In fact, sometimes serving the model to one population of devices is staightforward as companies may have different types of maintenance contracts with different end customers - one set of customers may continue with a no-maintenance contract plan where failure data would still be able to be collected. A new model could be tested on the combined population to verify the new model would have captured failures predicted by the original model. If the level of prediction is acceptable within some measure, the business could decide to replace the original model. 

Alternatively, some companies have internal lab testing environments, and collect data for predictive maintenance solutions through stress testing of the equipment over time in their labs. Of course, it is often difficult to replicate the real world effects so this data often needs to be supplemented.
 
# Conclusion

In this age of Artificial intelligence (AI) for everyone, much thought has been given to how to build and deploy machine learning solutions. This article details some of the work required both before and after the machine learning solution has been developed. 

  * Specifics about What data is required? Am I ready to develop an machine learning solution? Have I captured enough failure events to build a useful predictive maintenance solution?
  * What will be the actual product delivered? Once I have a solution, how can I use this to improve my maintenance strategy?
  * What should we do after we have brought these solutions into our business practice? How can I improve my solution going forward?

This article was written specifically for predictive maintenance settings when the goal of the model is to remove the events the model is predicting. However, many of these ideas are just as useful for any machine learning solution you are planning on bringing into your business practice.

Use intelligence when deploying an Artificial Intelligence solutions into your business practice.
