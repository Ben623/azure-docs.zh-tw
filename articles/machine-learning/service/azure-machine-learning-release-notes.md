---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning
description: Learn about the latest updates to Azure Machine Learning and the machine learning and data prep Python SDKs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: d2be8f7f2e8859301285e4b87cfb5da88e3317e3
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483365"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning release notes

In this article, learn about Azure Machine Learning releases.  For the full SDK reference content,  visit the Azure Machine Learning's [**main SDK for Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) reference page.

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK for Python v1.0.76

+ **重大變更**
  + Azureml-Train-AutoML upgrade issues
    + Upgrading to azureml-train-automl>=1.0.76 from azureml-train-automl<1.0.76 can cause partial installations, causing some automl imports to fail. To resolve this, you can run the setup script found at https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Or if you are using pip directly you can:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + or you can uninstall the old version before upgrading
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Bug fixes and improvements**
  + **azureml-automl-runtime**
    + AutoML will now take into account both true and false classes when calculating averaged scalar metrics for binary classification tasks.
    + Moved Machine learning and training code in AzureML-AutoML-Core to a new package AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + When calling `to_pandas_dataframe` on a labeled dataset with the download option, you can now specify whether to overwrite existing files or not.
    + When calling `keep_columns` or `drop_columns` that results in a timeseries, label, or image column being dropped, the corresponding capabilities will be dropped for the dataset as well.
    + Fixed an issue with pytorch loader for the object detection task.
  + **azureml-contrib-interpret**
    + Removed explanation dashboard widget from azureml-contrib-interpret, changed package to reference the new one in interpret_community
    + Updated version of interpret-community to 0.2.0
  + **azureml-core**
    + Improve performance of `workspace.datasets`.
    + Added the ability to register Azure SQL Database Datastore using username and password authentication
    + Fix for loading RunConfigurations from relative paths.
    + When calling `keep_columns` or `drop_columns` that results in a timeseries column being dropped, the corresponding capabilities will be dropped for the dataset as well.
  + **azureml-interpret**
    + updated version of interpret-community to 0.2.0
  + **azureml-pipeline-steps**
    + Documented supported values for `runconfig_pipeline_params` for azure machine learning pipeline steps.
  + **azureml-pipeline-core**
    + Added CLI option to download output in json format for Pipeline commands.
  + **azureml-train-automl**
    + Split AzureML-Train-AutoML into 2 packages, an client package AzureML-Train-AutoML-Client and a ML training package AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Added a thin client for submitting AutoML experiments without needing to install any machine learning dependencies locally.
    + Fixed logging of automatically detected lags, rolling window sizes and maximal horizons in the remote runs.
  + **azureml-train-automl-runtime**
    + Added a new AutoML package to isolate machine learning and runtime components from the client.
  + **azureml-contrib-train-rl**
    + Added reinforcement learning support in SDK.
    + Added AmlWindowsCompute support in RL SDK. 

 
## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK for Python v1.0.74
 
  + **預覽功能**
    + **azureml-contrib-dataset**
      + After importing azureml-contrib-dataset, you can call `Dataset.Labeled.from_json_lines` instead of `._Labeled` to create a labeled dataset.
      + When calling `to_pandas_dataframe` on a labeled dataset with the download option, you can now specify whether to overwrite existing files or not.
      + When calling `keep_columns` or `drop_columns` that results in a timeseries, label, or image column being dropped, the corresponding capabilities will be dropped for the dataset as well.
      + Fixed issues with PyTorch loader when calling `dataset.to_torchvision()`.

+ **Bug fixes and improvements**
  + **azure-cli-ml**
    + Added Model Profiling to the preview CLI.
    + Fixes breaking change in Azure Storage causing AzureML CLI to fail.
    + Added Load Balancer Type to MLC for AKS types
  + **azureml-automl-core**
    + Fixed the issue with detection of maximal horizon on time series, having missing values and multiple grains.
    + Fixed the issue with failures diring generation of cross validation splits.
    + Replace this section with a message in markdown format to appear in the release notes: -Improved handling of short grains in the forecasting data sets.
    + Fixed the issue with masking of some user information during logging. -Improved logging of the errors during forecasting runs.
    + Adding psutil as a conda dependency to the auto-generated yml deployment file.
  + **azureml-contrib-mir**
    + Fixes breaking change in Azure Storage causing AzureML CLI to fail.
  + **azureml-core**
    + Fixes a bug which caused models deployed on Azure Functions to produce 500s.
    + Fixed an issue where the amlignore file was not applied on snapshots.
    + Added a new API amlcompute.get_active_runs that returns a generator for running and queued runs on a given amlcompute.
    + Added Load Balancer Type to MLC for AKS types.
    + Added append_prefix bool parameter to download_files in run.py and download_artifacts_from_prefix in artifacts_client. This flag is used to selectively flatten the origin filepath so only the file or folder name is added to the output_directory
    + Fix deserialization issue for `run_config.yml` with dataset usage.
    + When calling `keep_columns` or `drop_columns` that results in a timeseries column being dropped, the corresponding capabilities will be dropped for the dataset as well.
  + **azureml-interpret**
    + Updated interpret-community version to 0.1.0.3
  + **azureml-train-automl**
    + Fixed an issue where automl_step might not print validation issues.
    + Fixed register_model to succeed even if the model's environment is missing dependencies locally.
    + Fixed an issue where some remote runs were not docker enabled.
    + Add logging of the exception that is causing a local run to fail prematurely.
  + **azureml-train-core**
    + Consider resume_from runs in the calculation of automated hyperparameter tuning best child runs.
  + **azureml-pipeline-core**
    + Fixed parameter handling in pipeline argument construction.
    + Added pipeline description and step type yaml parameter.
    + New yaml format for Pipeline step and added deprecation warning for old format.
    
    
  
## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Web experience 

The collaborative workspace landing page at [https://ml.azure.com](https://ml.azure.com) has been enhanced and rebranded as the Azure Machine Learning studio (preview).

From the studio, you can train, test, deploy, and manage Azure Machine Learning assets such as datasets, pipelines, models, endpoints, and more.  

Access the following web-based authoring tools from the studio:

| Web-based tool | 描述 | 版本 |
|-|-|-|
| Notebook VM(preview) | Fully managed cloud-based workstation | Basic & Enterprise |
| [Automated machine learning](tutorial-first-experiment-automated-ml.md) (preview) | No code experience for automating machine learning model development | Enterprise |
| [Designer](ui-concept-visual-interface.md) (preview) | Drag-and-drop machine learning modeling tool formerly known as the the designer | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning designer enhancements 

+ Formerly known as the visual interface 
+   11 new [modules](../algorithm-module-reference/module-reference.md) including recommenders, classifiers, and training utilities including feature engineering, cross validation, and data transformation.

### <a name="r-sdk"></a>R SDK 
 
Data scientists and AI developers use the [Azure Machine Learning SDK for R](tutorial-1st-r-experiment.md) to build and run machine learning workflows with Azure Machine Learning.

The Azure Machine Learning SDK for R uses the `reticulate` package to bind to the Python SDK. By binding directly to Python, the SDK for R allows you access to core objects and methods implemented in the Python SDK from any R environment you choose.

Main capabilities of the SDK include:

+   管理用來對您的機器學習實驗進行監視、記錄及組織的雲端資源。
+   Train models using cloud resources, including GPU-accelerated model training.
+   Deploy your models as webservices on Azure Container Instances (ACI) and Azure Kubernetes Service (AKS).

See the [package website](https://azure.github.io/azureml-sdk-for-r) for complete documentation.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK for Python v1.0.72

+ **新功能**
  + Added dataset monitors through the [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) package, allowing for monitoring timeseries datasets for data drift or other statistical changes over time. Alerts and events can be triggered if drift is detected or other conditions on the data are met. See [our documentation](https://aka.ms/datadrift) for details. 
  + Announcing two new editions (also referred to as a SKU interchangeably) in Azure Machine Learning. With this release you can now create either a Basic or Enterprise Azure ML workspace. All existing workspaces will be defaulted to the Basic edition, and you can go to the Azure portal or to the studio to upgrade the workspace anytime. You can create either a Basic or Enterprise workspace from the Azure Portal. Please read [our documentation](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) to learn more. From the SDK, the edition of your workspace can be determined using the "sku" property of your workspace object.
  + We have also made enhancements to Azure Machine Learning Compute - you can now view metrics for your clusters (like total nodes, running nodes, total core quota) in Azure Monitor, besides viewing Diagnostic logs for debugging. In addition you can also view currently running or queued runs on your cluster and details such as the IPs of the various nodes on your cluster. You can view these either in the portal or by using corresponding functions in the SDK or CLI. 
  
  + **預覽功能**
    + We are releasing preview support for disk encryption of your local SSD in Azure Machine Learning Compute. Please raise a technical support ticket to get your subscription whitelisted to use this feature.
    + Public Preview of Azure Machine Learning Batch Inference. Azure Machine Learning Batch Inference targets large inference jobs that are not time-sensitive. Batch Inference provides cost-effective inference compute scaling, with unparalleled throughput for asynchronous applications. It is optimized for high-throughput, fire-and-forget inference over large collections of data.  
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Enabled functionalities for labeled dataset
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask
        
        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)
        
        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()
        
        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD) 
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)
        
        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Bug fixes and improvements**
  + **azure-cli-ml**
    + CLI now supports model packaging.
    + Added dataset CLI. For more information: `az ml dataset --help`
    + Added support for deploying and packaging supported models (ONNX, scikit-learn, and TensorFlow) without an InferenceConfig instance.
    + Added overwrite flag for service deployment (ACI and AKS) in SDK and CLI. If provided, will overwrite the existing service if service with name already exists. If service doesn't exist, will create new service.
    + Models can be registered with two new frameworks, Onnx and Tensorflow. - Model registration accepts sample input data, sample output data and resource configuration for the model.
  + **azureml-automl-core**
    + Training an iteration would run in a child process only when runtime constraints are being set.
    + Added a guardrail for forecasting tasks, to check whether a specified max_horizon will cause a memory issue on the given machine or not. If it will, a guardrail message will be displayed.
    + Added support for complex frequencies like 2 years and 1 month. -Added comprehensible error message if frequency can not be determined.
    + Add azureml-defaults to auto generated conda env to solve the model deployment failure
    + Allow intermediate data in Azure Machine Learning Pipeline to be converted to tabular dataset and used in `AutoMLStep`.
    + Implemented column purpose update for streaming.
    + Implemented transformer parameter update for Imputer and HashOneHotEncoder for streaming.
    + Added the current data size and the minimum required data size to the validation error messages.
    + Updated the minimum required data size for Cross-validation to guarantee a minimum of two samples in each validation fold.
  + **azureml-cli-common**
    + CLI now supports model packaging.
    + Models can be registered with two new frameworks, Onnx and Tensorflow.
    + Model registration accepts sample input data, sample output data and resource configuration for the model.
  + **azureml-contrib-gbdt**
    + fixed the release channel for the notebook
    + Added a warning for non AmlCompute compute target that we don't support
    + Added LightGMB Estimator to azureml-contrib-gbdt package
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI now supports model packaging.
    + Add deprecation warning for deprecated Dataset APIs. See Dataset API change notice at https://aka.ms/tabular-dataset.
    + Change [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) to return registration name and version if the dataset is registered.
    + Fix a bug that ScriptRunConfig with dataset as argument cannot be used repeatedly to submit experiment run.
    + Datasets retrieved during a run will be tracked and can be seen in the run details page or by calling [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) after the run is complete.
    + Allow intermediate data in Azure Machine Learning Pipeline to be converted to tabular dataset and used in [`AutoMLStep`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep).
    + Added support for deploying and packaging supported models (ONNX, scikit-learn, and TensorFlow) without an InferenceConfig instance.
    + Added overwrite flag for service deployment (ACI and AKS) in SDK and CLI. If provided, will overwrite the existing service if service with name already exists. If service doesn't exist, will create new service.
    +  Models can be registered with two new frameworks, Onnx and Tensorflow. Model registration accepts sample input data, sample output data and resource configuration for the model.
    + Added new datastore for Azure Database for MySQL. Added example for using Azure Database for MySQL in DataTransferStep in Azure Machine Learning Pipelines.
    + Added functionality to add and remove tags from experiments Added functionality to remove tags from runs
    + Added overwrite flag for service deployment (ACI and AKS) in SDK and CLI. If provided, will overwrite the existing service if service with name already exists. If service doesn't exist, will create new service.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Moved from `azureml-contrib-datadrift` into `azureml-datadrift`
    + Added support for monitoring timeseries datasets for drift and other statistical measures 
    + New methods `create_from_model()` and `create_from_dataset()` to the [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) class. The `create()` method will be deprecated. 
    + Adjustments to the visualizations in Python and UI in the Azure Machine Learning studio.
    + Support weekly and monthly monitor scheduling, in addition to daily for dataset monitors.
    + Support backfill of data monitor metrics to analyze historical data for dataset monitors. 
    + 各種 Bug 修正 
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep is no longer needed to submit an Azure Machine Learning Pipeline run from the pipeline `yaml` file.
  + [**azureml-train-automl**](https://docs.microsoft.com/python/api/azureml-train-automl)
    + Add azureml-defaults to auto generated conda env to solve the model deployment failure
    + AutoML remote training now includes azureml-defaults to allow reuse of training env for inference.
  + **azureml-train-core**
    + Added PyTorch 1.3 support in [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimator
  
## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visual interface (preview)

+ The Azure Machine Learning visual interface (preview) has been overhauled to run on [Azure Machine Learning pipelines](concept-ml-pipelines.md). Pipelines (previously known as experiments) authored in the visual interface are now fully integrated with the core Azure Machine Learning experience.
  + Unified management experience with SDK assets
  + Versioning and tracking for visual interface models, pipelines, and endpoints 
  + Redesigned UI
  + Added batch inference deployment
  + Added Azure Kubernetes Service (AKS) support for inference compute targets
  + New Python-step pipeline authoring workflow
  + New [landing page](https://ml.azure.com) for visual authoring tools

+ **New modules**
  + Apply math operation
  + Apply SQL transformation
  + Clip values
  + Summarize data
  + Import from SQL database  

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK for Python v1.0.69

+ **Bug fixes and improvements**
  + **azureml-automl-core**
    + Limiting model explanations to best run rather than computing explanations for every run. Making this behavior change for local, remote and ADB.
    + Added support for on-demand model explanations for UI
    + Added psutil as a dependency of `automl` and included psutil as a conda dependency in amlcompute.
    + Fixed the issue with heuristic lags and rolling window sizes on the forecasting data sets some series of which can cause linear algebra errors
      + Added print out for the heuristically determined parameters in the forecasting runs.
  + **azureml-contrib-datadrift**
    + Added protection while creating output metrics if dataset level drift is not in the first section.
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model package has been renamed to azureml-contrib-interpret
  + **azureml-core**
    + Added API to unregister datasets. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model package has been renamed to azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Added API to unregister datasets. dataset.[unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Added Dataset API to check data changed time. `dataset.data_changed_time`答案中所述步驟，工作帳戶即會啟用。
    + Being able to consume `FileDataset` and `TabularDataset` as inputs to `PythonScriptStep`, `EstimatorStep`, and `HyperDriveStep` in Azure Machine Learning Pipeline
    + Performance of `FileDataset.mount` has been improved for folders with a large number of files
    + Being able to consume [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) and [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) as inputs to [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep), and [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) in Azure Machine Learning Pipeline.
    + Performance of FileDataset.[mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) has been improved for folders with a large number of files
    + Added URL to known error recommendations in run details.
    + Fixed a bug in run.get_metrics where requests would fail if a run had too many children
    + Fixed a bug in [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) where requests would fail if a run had too many children
    + Added support for authentication on Arcadia cluster.
    + Creating an Experiment object gets or creates the experiment in the Azure Machine Learning workspace for run history tracking. The experiment ID and archived time are populated in the Experiment object on creation. Example: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() and reactivate() are functions that can be called on an experiment to hide and restore the experiment from being shown in the UX or returned by default in a call to list experiments. If a new experiment is created with the same name as an archived experiment, you can rename the archived experiment when reactivating by passing a new name. There can only be one active experiment with a given name. Example: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Create new active experiment with the same name as the archived. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") The static method list() on Experiment can take a name filter and ViewType filter. ViewType values are "ACTIVE_ONLY", "ARCHIVED_ONLY" and "ALL" Example: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Support using environment for model deploy, and service update
  + **azureml-datadrift**
    + The show attribute of DataDriftDector class won't support optional argument 'with_details' any more. The show attribute will only present data drift coefficient and data drift contribution of feature columns.
    + DataDriftDetector attribute 'get_output' behavior changes:
      + Input parameter start_time, end_time are optional instead of mandatory;
      + Input specific start_time and/or end_time with a specific run_id in the same invoking will result in value error exception because they are mutually exclusive 
      + By input specific start_time and/or end_time, only results of scheduled runs will be returned; 
      + Parameter 'daily_latest_only' is deprecated.
    + Support retrieving Dataset-based Data Drift outputs.
  + **azureml-explain-model**
    + Renames AzureML-explain-model package to AzureML-interpret, keeping the old package for backwards compatibility for now
    + fixed `automl` bug with raw explanations set to classification task instead of regression by default on download from ExplanationClient
    + Add support for `ScoringExplainer` to be created directly using `MimicWrapper`
  + **azureml-pipeline-core**
    + Improved performance for large Pipeline creation
  + **azureml-train-core**
    + Added TensorFlow 2.0 support in TensorFlow Estimator
  + **azureml-train-automl**
    + Creating an [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) object gets or creates the experiment in the Azure Machine Learning workspace for run history tracking. The experiment id and archived time are populated in the Experiment object on creation. 範例：

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) and [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) are functions that can be called on an experiment to hide and restore the experiment from being shown in the UX or returned by default in a call to list experiments. If a new experiment is created with the same name as an archived experiment, you can rename the archived experiment when reactivating by passing a new name. There can only be one active experiment with a given name. 範例： 
        
        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        The static method [list()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) on Experiment can take a name filter and ViewType filter. ViewType values are "ACTIVE_ONLY", "ARCHIVED_ONLY" and "ALL". 範例： 
        
        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Support using environment for model deploy, and service update.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + The show attribute of [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) class won't support optional argument 'with_details' any more. The show attribute will only present data drift coefficient and data drift contribution of feature columns.
    + DataDriftDetector function [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) behavior changes:
      + Input parameter start_time, end_time are optional instead of mandatory;
      + Input specific start_time and/or end_time with a specific run_id in the same invoking will result in value error exception because they are mutually exclusive; 
      + By input specific start_time and/or end_time, only results of scheduled runs will be returned; 
      + Parameter 'daily_latest_only' is deprecated.
    + Support retrieving Dataset-based Data Drift outputs.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Renames AzureML-explain-model package to AzureML-interpret, keeping the old package for backwards compatibility for now.
    + fixed automl bug with raw explanations set to classification task instead of regression by default on download from ExplanationClient.
    + Add support for [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) to be created directly using [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Improved performance for large Pipeline creation.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Added TensorFlow 2.0 support in [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + The parent run will no longer be failed when setup iteration failed, as the orchestration already takes care of it.
    + Added local-docker and local-conda support for AutoML experiments
    + Added local-docker and local-conda support for AutoML experiments.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>New web experience (preview) for Azure Machine Learning workspaces

The Experiment tab in the [new workspace portal](https://ml.azure.com) has been been updated so data scientists can monitor experiments in a more performant way. You can explore the following features:
+ Experiment metadata to easily filter and sort your list of experiments
+ Simplified and performant experiment details pages which allow you to visualize and compare your runs
+ New design to run details pages to understand and monitor your training runs

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK for Python v1.0.65

  + **新功能**
    + Added curated environments. These environments have been pre-configured with libraries for common machine learning tasks, and have been pre-build and cached as Docker images for faster execution. They appear by default in Workspace's list of environment, with prefix "AzureML".
    + Added curated environments. These environments have been pre-configured with libraries for common machine learning tasks, and have been pre-build and cached as Docker images for faster execution. They appear by default in [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)'s list of environment, with prefix "AzureML".
  
  + **azureml-train-automl**
  + **[azureml-train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + Added the ONNX conversion support for the ADB and HDI

+ **預覽功能**  
  + **azureml-train-automl**
  + **[azureml-train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + Supported BERT and BiLSTM as text featurizer (preview only)
    + Supported featurization customization for column purpose and transformer parameters (preview only)
    + Supported raw explanations when user enables model explanation during training (preview only)
    + Added Prophet for `timeseries` forecasting as a trainable pipeline (preview only)
  
  + **azureml-contrib-datadrift**
    + Packages relocated from azureml-contrib-datadrift to azureml-datadrift; the `contrib` package will be removed in a future release 

+ **Bug fixes and improvements**
  + **azureml-automl-core**
    + Introduced FeaturizationConfig to AutoMLConfig and AutoMLBaseSettings
    + Introduced FeaturizationConfig to [AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig) and AutoMLBaseSettings
      + Override Column Purpose for Featurization with given column and feature type
      + Override transformer parameters
    + Added deprecation message for explain_model() and retrieve_model_explanations()
    + Added Prophet as a trainable pipeline (preview only)
    + Added deprecation message for explain_model() and retrieve_model_explanations().
    + Added Prophet as a trainable pipeline (preview only).
    + Added support for automatic detection of target lags, rolling window size and maximal horizon. If one of target_lags, target_rolling_window_size or max_horizon is set to 'auto', the heuristics will be applied to estimate the value of corresponding parameter based on training data.
    + Fixed forecasting in the case when data set contains one grain column, this grain is of a numeric type and there is a gap between train and test set
    + Fixed the error message about the duplicated index in the remote run in forecasting tasks
    + Fixed forecasting in the case when data set contains one grain column, this grain is of a numeric type and there is a gap between train and test set.
    + Fixed the error message about the duplicated index in the remote run in forecasting tasks.
    + Added a guardrail to check whether a dataset is imbalanced or not. If it is, a guardrail message would be written to the console.
  + **azureml-core**
    + Added ability to retrieve SAS URL to model in storage through the model object. Ex: model.get_sas_url()
    + Introduce `run.get_details()['datasets']` to get datasets associated with the submitted run
    + Add API `Dataset.Tabular.from_json_lines_files` to create a TabularDataset from JSON Lines files. To learn about this tabular data in JSON Lines files on TabularDataset, please visit https://aka.ms/azureml-data for documentation.
    + Added additional VM size fields (OS Disk, number of GPUs) to the supported_vmsizes () function
    + Added additional fields to the list_nodes () function to show the run, the private and the public IP, the port etc.
    + Ability to specify a new field during cluster provisioning --remotelogin_port_public_access which can be set to enabled or disabled depending on whether you would like to leave the SSH port open or closed at the time of creating the cluster. If you do not specify it, the service will smartly open or close the port depending on whether you are deploying the cluster inside a VNet.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Added ability to retrieve SAS URL to model in storage through the model object. Ex: model.[get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduce run.[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] to get datasets associated with the submitted run
    + Add API `Dataset.Tabular`.[from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) to create a TabularDataset from JSON Lines files. To learn about this tabular data in JSON Lines files on TabularDataset, please visit https://aka.ms/azureml-data for documentation.
    + Added additional VM size fields (OS Disk, number of GPUs) to the [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) function
    + Added additional fields to the [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) function to show the run, the private and the public IP, the port etc.
    + Ability to specify a new field during cluster [provisioning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` which can be set to enabled or disabled depending on whether you would like to leave the SSH port open or closed at the time of creating the cluster. If you do not specify it, the service will smartly open or close the port depending on whether you are deploying the cluster inside a VNet.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Improved documentation for Explanation outputs in the classification scenario.
    + Added the ability to upload the predicted y values on the explanation for the evaluation examples. Unlocks more useful visualizations.
    + Added explainer property to MimicWrapper to enable getting the underlying MimicExplainer.
  + **azureml-pipeline-core**
    + Added notebook to describe Module, ModuleVersion and ModuleStep
  + **azureml-pipeline-steps**
    + Added RScriptStep to support R script run via AML pipeline
    + Fixed metadata parameters parsing in AzureBatchStep which was causing the error message "assignment for parameter SubscriptionId is not specified"
  + **azureml-train-automl**
    + Supported training_data, validation_data, label_column_name, weight_column_name as data input format
    + Added deprecation message for explain_model() and retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Added a [notebook](https://aka.ms/pl-modulestep) to describe [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) and [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Added [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) to support R script run via AML pipeline.
    + Fixed metadata parameters parsing in [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) which was causing the error message "assignment for parameter SubscriptionId is not specified".
  + **[azureml-train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + Supported training_data, validation_data, label_column_name, weight_column_name as data input format.
    + Added deprecation message for [explain_model()](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) and [retrieve_model_explanations()](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer#retrieve-model-explanation-child-run-).

  
## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK for Python v1.0.62

+ **新功能**
  + Introduced the `timeseries`  trait on TabularDataset. This trait enables easy timestamp filtering on data a TabularDataset, such as taking all data between a range of time or the most recent data. To learn about this the `timeseries`  trait on TabularDataset, please visit https://aka.ms/azureml-data for documentation or https://aka.ms/azureml-tsd-notebook for an example notebook. 
  + Enabled training with TabularDataset and FileDataset. Please visit https://aka.ms/dataset-tutorial for an example notebook. 
  
  + **azureml-train-core**
    + Added `Nccl` and `Gloo` support in PyTorch estimator
  
+ **Bug fixes and improvements**
  + **azureml-automl-core**
    + Deprecated the AutoML setting 'lag_length' and the LaggingTransformer.
    + Fixed correct validation of input data if they are specified in a Dataflow format
    + Modified the fit_pipeline.py to generate the graph json and upload to artifacts. 
    + Rendered the graph under `userrun` using `Cytoscape`.
  + **azureml-core**
    + Revisited the exception handling in ADB code and make changes to as per new error handling
    + Added automatic MSI authentication for Notebook VMs.
    + Fixes bug where corrupt or empty models could be uploaded because of failed retries.
    + Fixed the bug where `DataReference` name changes when the `DataReference` mode changes (e.g. when calling `as_upload`, `as_download`, or `as_mount`).
    + Make `mount_point` and `target_path` optional for `FileDataset.mount` and `FileDataset.download`.
    + Exception that timestamp column cannot be found will be throw out if the time serials related API is called without fine timestamp column assigned or the assigned timestamp columns are dropped.
    + Time serials columns should be assigned with column whose type is Date, otherwise exception is expected
    + Time serials columns assigning API 'with_timestamp_columns' can take None value fine/coarse timestamp column name, which will clear previously assigned timestamp columns.
    + Exception will be thrown out when either coarse grain or fine grained timestamp column is dropped with indication for user that dropping can be done after either excluding timestamp column in dropping list or call with_time_stamp with None value to release timestamp columns
    + Exception will be thrown out when either coarse grain or fine grained timestamp column is not included in keep columns list with indication for user that keeping can be done after either including timestamp column in keep column list or call with_time_stamp with None value to release timestamp columns.
    + Added logging for the size of a registered model.
  + **azureml-explain-model**
    + Fixed warning printed to console when "packaging" python package is not installed: "Using older than supported version of lightgbm, please upgrade to version greater than 2.2.1"
    + Fixed download model explanation with sharding for global explanations with many features
    + Fixed mimic explainer missing initialization examples on output explanation
    + Fixed immutable error on set properties when uploading with explanation client using two different types of models
    + Added a get_raw param to scoring explainer .explain() so one scoring explainer can return both engineered and raw values.
  + **azureml-train-automl**
    + Introduced public APIs from AutoML for supporting explanations from `automl` explain SDK - Newer way of supporting AutoML explanations by decoupling AutoML featurization and explain SDK - Integrated raw explanation support from azureml explain SDK for AutoML models.
    + Removing azureml-defaults from remote training environments.
    + Changed default cache store location from FileCacheStore based one to AzureFileCacheStore one for AutoML on Azure Databricks code path.
    + Fixed correct validation of input data if they are specified in a Dataflow format
  + **azureml-train-core**
    + Reverted source_directory_data_store deprecation.
    + Added ability to override azureml installed package versions. 
    + Added dockerfile support in `environment_definition` parameter in estimators.
    + Simplified distributed training parameters in estimators.

         ```py 
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer 
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>New web experience (preview) for Azure Machine Learning workspaces 
The new web experience enables data scientists and data engineers to complete their end-to-end machine learning lifecycle from prepping and visualizing data to training and deploying models in a single location. 

![Azure Machine Learning workspace UI (preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Key features:**

Using this new Azure Machine Learning interface, you can now:
+ Manage your notebooks or link out to Jupyter
+ [Run automated ML experiments](tutorial-first-experiment-automated-ml.md)
+ [Create datasets from local files, datastores, & web files](how-to-create-register-datasets.md)
+ Explore & prepare datasets for model creation
+ Monitor data drift for your models 
+ View recent resources from a dashboard

At the time of this release, the following browsers are supported: Chrome, Firefox, Safari, and Microsoft Edge Preview.

**已知問題︰**

1. Refresh your browser if you see “Something went wrong! Error loading chunk files” when deployment is in progress.  

1. Can’t delete or rename file in Notebooks and Files. During Public Preview you can use Jupyter UI or Terminal in Notebook VM to perform update file operations. Because it is a mounted network file system all changes you make on Notebook VM are immediately reflected in the Notebook Workspace. 

1. To SSH into the Notebook VM:
   1. Find the SSH keys  that were created during VM setup. Or, find the keys in  the Azure ML Azure portal > open Compute tab > locate Notebook VM in the list > open it’s properties : copy the keys from the dialog.
   1. Import those public and private SSH keys to your local machine.
   1. Use them to SSH into the Notebook VM. 

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK for Python v1.0.60

+ **新功能**
  + Introduced FileDataset, which references single or multiple files in your datastores or public urls. The files can be of any format. FileDataset provides you with the ability to download or mount the files to your compute. To learn about FileDataset, please visit https://aka.ms/file-dataset.
  + Added Pipeline Yaml Support for PythonScript Step, Adla Step, Databricks Step, DataTransferStep, and AzureBatch Step

+ **Bug fixes and improvements**
  + **azureml-automl-core**
    + AutoArima is now a suggestable pipeline for preview only.
    + Improved error reporting for forecasting.
    + Improved the logging by using custom exceptions instead of generic in the forecasting tasks.
    + Removed the check on max_concurrent_iterations to be less than total number of iterations.
    + AutoML models now return AutoMLExceptions
    + This release improves the execution performance of automated machine learning local runs.
  + **azureml-core**
    + Introduce Dataset.get_all(workspace), which returns a dictionary of `TabularDataset` and `FileDataset` objects keyed by their registration name. 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + Introduce `parition_format` as argument to `Dataset.Tabular.from_delimited_files` and `Dataset.Tabular.from_parquet.files`. The partition information of each data path will be extracted into columns based on the specified format. '{column_name}' creates string column, and '{column_name:yyyy/MM/dd/HH/mm/ss}' creates datetime column, where 'yyyy', 'MM', 'dd', 'HH', 'mm' and 'ss' are used to extract year, month, day, hour, minute, and second for the datetime type. The partition_format should start from the position of first partition key until the end of file path. For example, given the path '../USA/2019/01/01/data.csv' where the partition is by country and time, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' creates string column 'Country' with value 'USA' and datetime column 'PartitionDate' with value '2019-01-01'.
        ```py 
        workspace = Workspace.from_config() 
        all_datasets = Dataset.get_all(workspace) 
        mydata = all_datasets['my-data'] 
        ```
        
    + Introduce `partition_format` as argument to `Dataset.Tabular.from_delimited_files` and `Dataset.Tabular.from_parquet.files`. The partition information of each data path will be extracted into columns based on the specified format. '{column_name}' creates string column, and '{column_name:yyyy/MM/dd/HH/mm/ss}' creates datetime column, where 'yyyy', 'MM', 'dd', 'HH', 'mm' and 'ss' are used to extract year, month, day, hour, minute, and second for the datetime type. The partition_format should start from the position of first partition key until the end of file path. For example, given the path '../USA/2019/01/01/data.csv' where the partition is by country and time, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' creates string column 'Country' with value 'USA' and datetime column 'PartitionDate' with value '2019-01-01'.
    + `to_csv_files` and `to_parquet_files` methods have been added to `TabularDataset`. These methods enable conversion between a `TabularDataset` and a `FileDataset` by converting the data to files of the specified format.
    + Automatically log into the base image registry when saving a Dockerfile generated by Model.package().
    + 'gpu_support' is no longer necessary; AzureML now automatically detects and uses the nvidia docker extension when it is available. It will be removed in a future release.
    + Added support to create, update, and use PipelineDrafts.
    + This release improves the execution performance of automated machine learning local runs.
    + Users can query metrics from run history by name.
    + Improved the logging by using custom exceptions instead of generic in the forecasting tasks.
  + **azureml-explain-model**
    + Added feature_maps parameter to the new MimicWrapper, allowing users to get raw feature explanations.
    + Dataset uploads are now off by default for explanation upload, and can be re-enabled with upload_datasets=True
    + Added "is_law" filtering parameters to explanation list and download functions.
    + Adds method `get_raw_explanation(feature_maps)` to both global and local explanation objects.
    + Added version check to lightgbm with printed warning if below supported version
    + Optimized memory usage when batching explanations
    + AutoML models now return AutoMLExceptions
  + **azureml-pipeline-core**
    + Added support to create, update, and use PipelineDrafts - can be used to maintain mutable pipeline definitions and use them interactively to run
  + **azureml-train-automl**
    + Created feature to install specific versions of gpu-capable pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, pytorch-transformers, which is required to enable BERT/ XLNet in the remote python runtime environment.
  + **azureml-train-core**
    + Early failure of some hyperparameter space definition errors directly in the sdk instead of server side.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Bug fixes and improvements**
  + Enabled writing to ADLS/ADLSGen2 using raw path and credentials.
  + Fixed a bug that caused `include_path=True` to not work for `read_parquet`.
  + Fixed `to_pandas_dataframe()` failure caused by exception "Invalid property value: hostSecret".
  + Fixed a bug where files could not be read on DBFS in Spark mode.
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK for Python v1.0.57
+ **新功能**
  + Enabled `TabularDataset` to be consumed by AutomatedML. To learn more about `TabularDataset`, please visit https://aka.ms/azureml/howto/createdatasets.
  
+ **Bug fixes and improvements**
  + **automl-client-core-nativeclient**
    + Fixed the error, raised when training and/or validation labels (y and y_valid) are provided in the form of pandas dataframe but not as numpy array.
    + Updated interface to create a `RawDataContext` to only require the data and the `AutoMLBaseSettings` object.
    +  Allow AutoML users to drop training series that are not long enough when forecasting. - Allow AutoML users to drop grains from the test set that does not exist in the training set when forecasting.
  + **azure-cli-ml**
    + You can now update the SSL certificate for the scoring endpoint deployed on AKS cluster both for Microsoft generated and customer certificate.
  + **azureml-automl-core**
    + Fixed an issue in AutoML where rows with missing labels were not removed properly.
    + Improved error logging in AutoML; full error messages will now always be written to the log file.
    + AutoML has updated its package pinning to include `azureml-defaults`, `azureml-explain-model`, and `azureml-dataprep`. AutoML will no longer warn on package mismatches (except for `azureml-train-automl` package).
    + Fixed an issue in `timeseries`  where cv splits are of unequal size causing bin calculation to fail.
    + When running ensemble iteration for the Cross-Validation training type, if we ended up having trouble downloading the models trained on the entire dataset, we were having an inconsistency between the model weights and the models that were being fed into the voting ensemble.
    + Fixed the error, raised when training and/or validation labels (y and y_valid) are provided in the form of pandas dataframe but not as numpy array.
    + Fixed the issue with the forecasting tasks when None was encountered in the Boolean columns of input tables.
    + Allow AutoML users to drop training series that are not long enough when forecasting. - Allow AutoML users to drop grains from the test set that does not exist in the training set when forecasting.
  + **azureml-core**
    + Fixed issue with blob_cache_timeout parameter ordering.
    + Added external fit and transform exception types to system errors.
    + Added support for Key Vault secrets for remote runs. Add a azureml.core.keyvault.Keyvault class to add, get, and list secrets from the keyvault associated with your workspace. Supported operations are:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(name, value)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Additional methods to obtain default keyvault and get secrets during remote run:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Added additional override parameters to submit-hyperdrive CLI command.
    + Improve reliability of API calls be expanding retries to common requests library exceptions.
    + Add support for submitting runs from a submitted run.
    + Fixed expiring SAS token issue in FileWatcher, which caused files to stop being uploaded after their initial token had expired.
    + Supported importing HTTP csv/tsv files in dataset python SDK.
    + Deprecated the Workspace.setup() method. Warning message shown to users suggests using create() or get()/from_config() instead.
    + Added Environment.add_private_pip_wheel(), which enables uploading private custom python packages `whl`to the workspace and securely using them to build/materialize the environment.
    + You can now update the SSL certificate for the scoring endpoint deployed on AKS cluster both for Microsoft generated and customer certificate.
  + **azureml-explain-model**
    + Added parameter to add a model ID to explanations on upload.
    + Added `is_raw` tagging to explanations in memory and upload.
    + Added pytorch support and tests for azureml-explain-model package.
  + **azureml-opendatasets**
    + Support detecting and logging auto test environment.
    + Added classes to get US population by county and zip.
  + **azureml-pipeline-core**
    + Added label property to input and output port definitions.
  + **azureml-telemetry**
    + Fixed an incorrect telemetry configuration.
  + **azureml-train-automl**
    + Fixed the bug where on setup failure, error was not getting logged in "errors" field for the setup run and hence was not stored in parent run "errors".
    + Fixed an issue in AutoML where rows with missing labels were not removed properly.
    + Allow AutoML users to drop training series that are not long enough when forecasting.
    + Allow AutoML users to drop grains from the test set that do not exist in the training set when forecasting.
    + Now AutoMLStep passes through `automl` config to backend to avoid any issues on changes or additions of new config parameters.
    + AutoML Data Guardrail is now in public preview. User will see a Data Guardrail report (for classification/regression tasks) after training and also be able to access it through SDK API.
  + **azureml-train-core**
    + Added torch 1.2 support in PyTorch Estimator.
  + **azureml-widgets**
    + Improved confusion matrix charts for classification training.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **新功能**
  + Lists of strings can now be passed in as input to `read_*` methods.

+ **Bug fixes and improvements**
  + The performance of `read_parquet` has been significantly improved when running in Spark.
  + Fixed an issue where `column_type_builder` failed in case of a single column with ambiguous date formats.

### <a name="azure-portal"></a>Azure Portal
+ **Preview Feature**
  + Log and output file streaming is now available for run details pages. The files will stream updates in real time when the preview toggle is turned on.
  + Ability to set quota at a workspace level is released in preview. AmlCompute quotas are allocated at the subscription level, but we now allow you to distribute that quota between workspaces and allocate it for fair sharing and governance. Just click on the **Usages+Quotas** blade in the left navigation bar of your workspace and select the **Configure Quotas** tab. Note that you must be a subscription admin to be able to set quotas at the workspace level since this is a cross-workspace operation.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK for Python v1.0.55

+ **新功能**
  + Token based authentication is now supported for the calls made to the scoring endpoint deployed on AKS. We will continue to support the current key based authentication and users can use one of these authentication mechanisms at a time.
  + Ability to register a blob storage that is behind the virtual network (VNet) as a datastore.
  
+ **Bug fixes and improvements**
  + **azureml-automl-core**
    + Fixes a bug where validation size for CV splits is small and results in bad predicted vs. true charts for regression and forecasting.
    + The logging of forecasting tasks on the remote runs improved, now user is provided with comprehensive error message if the run was failed.
    + Fixed failures of `Timeseries` if preprocess flag is True.
    + Made some forecasting data validation error messages more actionable.
    + Reduced memory consumption of AutoML runs by dropping and/or lazy loading of datasets, especially in between process spawns
  + **azureml-contrib-explain-model**
    + Added model_task flag to explainers to allow user to override default automatic inference logic for model type
    + Widget changes: Automatically installs with `contrib`, no more `nbextension` install/enable - support explanation with just global feature importance (eg Permutative)
    + Dashboard changes: - Box plots and violin plots in addition to `beeswarm` plot on summary page - Much faster rerendering of `beeswarm` plot on 'Top -k' slider change - helpful message explaining how top-k is computed - Useful customizable messages in place of charts when data not provided
  + **azureml-core**
    + Added Model.package() method to create Docker images and Dockerfiles that encapsulate models and their dependencies.
    + Updated local webservices to accept InferenceConfigs containing Environment objects.
    + Fixed Model.register() producing invalid models when '.' (for the current directory) is passed as the model_path parameter.
    + Add Run.submit_child, the functionality mirrors Experiment.submit while specifying the run as the parent of the submitted child run.
    + Support configuration options from Model.register in Run.register_model.
    + Ability to run JAR jobs on existing cluster.
    + Now supporting instance_pool_id and cluster_log_dbfs_path parameters.
    + Added support for using an Environment object when deploying a Model to a Webservice. The Environment object can now be provided as a part of the InferenceConfig object.
    + Add appinsifht mapping for new regions - centralus - westus - northcentralus
    + Added documentation for all the attributes in all the Datastore classes.
    + Added blob_cache_timeout parameter to `Datastore.register_azure_blob_container`.
    + Added save_to_directory and load_from_directory methods to azureml.core.environment.Environment.
    + Added the "az ml environment download" and "az ml environment register" commands to the CLI.
    + Added Environment.add_private_pip_wheel method.
  + **azureml-explain-model**
    + Added dataset tracking to Explanations using the Dataset service (preview).
    + Decreased default batch size when streaming global explanations from 10k to 100.
    + Added model_task flag to explainers to allow user to override default automatic inference logic for model type.
  + **azureml-mlflow**
    + Fixed bug in mlflow.azureml.build_image where nested directories are ignored.
  + **azureml-pipeline-steps**
    + Added ability to run JAR jobs on existing Azure Databricks cluster.
    + Added support instance_pool_id and cluster_log_dbfs_path parameters for DatabricksStep step.
    + Added support for pipeline parameters in DatabricksStep step.
  + **azureml-train-automl**
    + Added `docstrings` for the Ensemble related files.
    + Updated docs to more appropriate language for `max_cores_per_iteration` and `max_concurrent_iterations`
    + The logging of forecasting tasks on the remote runs improved, now user is provided with comprehensive error message if the run was failed.
    + Removed get_data from pipeline `automlstep` notebook.
    + Started support `dataprep` in `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **新功能**
  + You can now request to execute specific inspectors (e.g. histogram, scatter plot, etc.) on specific columns.
  + Added a parallelize argument to `append_columns`. If True, data will be loaded into memory but execution will run in parallel; if False, execution will be streaming but single-threaded.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK for Python v1.0.53

+ **新功能**
  + Automated Machine Learning now supports training ONNX models on the remote compute target
  + Azure Machine Learning now provides ability to resume training from a previous run, checkpoint or model files.
    + Learn how to [use estimators to resume training from a previous run](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug fixes and improvements**
  + **automl-client-core-nativeclient**
    + Fix the bug about loosing columns types after the transformation (bug linked); 
    + Allow y_query to be an object type containing None(s) at the begin (#459519).
  + **azure-cli-ml**
    + CLI commands "model deploy" and "service update" now accept parameters, config files, or a combination of the two. Parameters have precedence over attributes in files.
    + Model description can now be updated after registration
  + **azureml-automl-core**
    + Update NimbusML dependency to 1.2.0 version (current latest).
    + Adding support for Nimbus ML estimators & pipelines to be used within AutoML estimators.
    + Fixing a bug in the Ensemble selection procedure which was unnecessarily growing the resulting ensemble even if the scores remained constant.
    + Enable re-use of some featurizations across CV Splits for forecasting tasks. This speeds up the run-time of the setup run by roughly a factor of n_cross_validations for expensive featurizations like lags and rolling windows.
    + Addressing an issue if time is out of pandas supported time range. We now raise a DataException if time is less than pd.Timestamp.min or greater than pd.Timestamp.max
    + Forecasting now allows different frequencies in train and test sets if they can be aligned. For example,  “quarterly starting in January” and at “quarterly starting in October” can be aligned.
    + The property "parameters" was added to the TimeSeriesTransformer.
    + Remove old exception classes.
    + In forecasting tasks, the `target_lags` parameter now accepts a single integer value or a list of integers. If the integer was provided, only one lag will be created. If a list is provided, the unique values of lags will be taken. target_lags=[1, 2, 2, 4] will create lags of one, 2 and 4 periods.
    + Fix the bug about losing columns types after the transformation (bug linked);
    + In `model.forecast(X, y_query)`, allow y_query to be an object type containing None(s) at the begin (#459519).
    + Add expected values to `automl` output
  + **azureml-contrib-datadrift**
    +  Improvements to example notebook including switch to azureml-opendatasets instead of azureml-contrib-opendatasets and performance improvements when enriching data
  + **azureml-contrib-explain-model**
    + Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package
    + added segmentations to image explanations in image explainer for AzureML-contrib-explain-model package
    + add scipy sparse support for LimeExplainer
    + added `batch_size` to mimic explainer when `include_local=False`, for streaming global explanations in batches to improve execution time of DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Fix for calling set_featurizer_timeseries_params(): dict value type change and null check - Add notebook for `timeseries`  featurizer
    + Update NimbusML dependency to 1.2.0 version (current latest).
  + **azureml-core**
    + Added the ability to attach DBFS datastores in the AzureML CLI 
    + Fixed the bug with datastore upload where an empty folder is created if `target_path` started with `/`
    + Fixed `deepcopy` issue in ServicePrincipalAuthentication.
    + Added the "az ml environment show" and "az ml environment list" commands to the CLI.
    + Environments now support specifying a base_dockerfile as an alternative to an already-built base_image.
    + The unused RunConfiguration setting auto_prepare_environment has been marked as deprecated.
    + Model description can now be updated after registration
    + Bugfix: Model and Image delete now provides more information about retrieving upstream objects that depend on them if delete fails due to an upstream dependency.
    + Fixed bug that printed blank duration for deployments that occur when creating a workspace for some environments.
    + Improved workspace create failure exceptions. Such that users don't see "Unable to create workspace. Unable to find..." as the message and instead see the actual creation failure.
    + Add support for token authentication in AKS webservices. 
    + Add `get_token()` method to `Webservice` objects.
    + Added CLI support to manage machine learning datasets.
    + `Datastore.register_azure_blob_container` now optionally takes a `blob_cache_timeout` value (in seconds) which configures blobfuse's mount parameters to enable cache expiration for this datastore. The default is no timeout, i.e. when a blob is read, it will stay in the local cache until the job is finished. Most jobs will prefer this setting, but some jobs need to read more data from a large dataset than will fit on their nodes. For these jobs, tuning this parameter will help them succeed. Take care when tuning this parameter: setting the value too low can result in poor performance, as the data used in an epoch may expire before being used again. This means that all reads will be done from blob storage (i.e. the network) rather than the local cache, which negatively impacts training times.
    + Model description can now properly be updated after registration
    + Model and Image deletion now provides more information about upstream objects that depend on them which causes the delete to fail
    + Improve resource utilization of remote runs using azureml.mlflow.
  + **azureml-explain-model**
    + Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package
    + add scipy sparse support for LimeExplainer
    + added shape linear explainer wrapper, as well as another level to tabular explainer for explaining linear models
    + for mimic explainer in explain model library, fixed error when include_local=False for sparse data input
    + add expected values to `automl` output
    + fixed permutation feature importance when transformations argument supplied to get raw feature importance
    + added `batch_size` to mimic explainer when `include_local=False`, for streaming global explanations in batches to improve execution time of DecisionTreeExplainableModel
    + for model explainability library, fixed blackbox explainers where pandas dataframe input is required for prediction
    + Fixed a bug where `explanation.expected_values` would sometimes return a float rather than a list with a float in it.
  + **azureml-mlflow**
    + Improve performance of mlflow.set_experiment(experiment_name)
    + Fix bug in use of InteractiveLoginAuthentication for mlflow tracking_uri
    + Improve resource utilization of remote runs using azureml.mlflow.
    + Improve the documentation of the azureml-mlflow package
    + Patch bug where mlflow.log_artifacts("my_dir") would save artifacts under "my_dir/<artifact-paths>" instead of "<artifact-paths>"
  + **azureml-opendatasets**
    + Pin `pyarrow` of `opendatasets` to old versions (<0.14.0) because of memory issue newly introduced there.
    +  Move azureml-contrib-opendatasets to azureml-opendatasets. - Allow open dataset classes to be registered to AML workspace and leverage AML Dataset capabilities seamlessly. - Improve NoaaIsdWeather enrich performance in non-SPARK version significantly.
  + **azureml-pipeline-steps**
    + DBFS Datastore is now supported for Inputs and Outputs in DatabricksStep.
    + Updated documentation for Azure Batch Step with regards to inputs/outputs.
    + In AzureBatchStep, changed *delete_batch_job_after_finish* default value to *true*.
  + **azureml-telemetry**
    +  Move azureml-contrib-opendatasets to azureml-opendatasets. - Allow open dataset classes to be registered to AML workspace and leverage AML Dataset capabilities seamlessly. - Improve NoaaIsdWeather enrich performance in non-SPARK version significantly.
  + **azureml-train-automl**
    + Updated documentation on get_output to reflect the actual return type and provide additional notes on retrieving key properties.
    + Update NimbusML dependency to 1.2.0 version (current latest).
    + add expected values to `automl` output
  + **azureml-train-core**
    + Strings are now accepted as compute target for Automated Hyperparameter Tuning
    + The unused RunConfiguration setting auto_prepare_environment has been marked as deprecated.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **新功能**
  + Added support for reading a file directly from a http or https url.

+ **Bug fixes and improvements**
  + Improved error message when attempting to read a Parquet Dataset from a remote source (which is not currently supported).
  + Fixed a bug when writing to Parquet file format in ADLS Gen 2, and updating the ADLS Gen 2 container name in the path.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visual Interface
+ **預覽功能**
  + Added "Execute R script" module in visual interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** is now available as **azureml-opendatasets**. The old package can still work, but we recommend you using **azureml-opendatasets** moving forward for richer capabilities and improvements.
    + This new package allows you to register open datasets as Dataset in AML workspace, and leverage whatever functionalities that Dataset offers.
    + It also includes existing capabilities such as consuming open datasets as Pandas/SPARK dataframes, and location joins for some dataset like weather.

+ **預覽功能**
    + HyperDriveConfig can now accept pipeline object as a parameter to support hyperparameter tuning using a pipeline.

+ **Bug fixes and improvements**
  + **azureml-train-automl**
    + Fixed the bug about losing columns types after the transformation.
    + Fixed the bug to allow y_query to be an object type containing None(s) at the beginning. 
    + Fixed the issue in the Ensemble selection procedure which was unnecessarily growing the resulting ensemble even if the scores remained constant.
    + Fixed the issue with whitelist_models and blacklist_models settings in AutoMLStep.
    + Fixed the issue that prevented the usage of preprocessing when AutoML would have been used in the context of Azure ML Pipelines.
  + **azureml-opendatasets**
    + Moved azureml-contrib-opendatasets to azureml-opendatasets.
    + Allowed open dataset classes to be registered to AML workspace and leverage AML Dataset capabilities seamlessly.
    + Improved NoaaIsdWeather enrich performance in non-SPARK version significantly.
  + **azureml-explain-model**
    + Updated online documentation for interpretability objects.
    + Added `batch_size` to mimic explainer when `include_local=False`, for streaming global explanations in batches to improve execution time of DecisionTreeExplainableModel for model explainability library.
    + Fixed the issue where `explanation.expected_values` would sometimes return a float rather than a list with a float in it.
    + Added expected values to `automl` output for mimic explainer in explain model library.
    + Fixed permutation feature importance when transformations argument supplied to get raw feature importance.
  + **azureml-core**
    + Added the ability to attach DBFS datastores in the AzureML CLI.
    + Fixed the issue with datastore upload where an empty folder is created if `target_path` started with `/`.
    + Enabled comparison of two datasets.
    + Model and Image delete now provides more information about retrieving upstream objects that depend on them if delete fails due to an upstream dependency.
    + Deprecated the unused RunConfiguration setting in auto_prepare_environment.
  + **azureml-mlflow**
    + Improved resource utilization of remote runs that use azureml.mlflow.
    + Improved the documentation of the azureml-mlflow package.
    + Fixed the issue where mlflow.log_artifacts("my_dir") would save artifacts under "my_dir/artifact-paths" instead of "artifact-paths".
  + **azureml-pipeline-core**
    + Parameter hash_paths for all pipeline steps is deprecated and will be removed in future. By default contents of the source_directory is hashed (except files listed in .amlignore or .gitignore)
    + Continued improving Module and ModuleStep to support compute type specific modules, to prepare for RunConfiguration integration and other changes to unlock compute type specific module usage in pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Improved documentation with regards to inputs/outputs.
    + AzureBatchStep: Changed delete_batch_job_after_finish default value to true.
  + **azureml-train-core**
    + Strings are now accepted as compute target for Automated Hyperparameter Tuning.
    + Deprecated the unused RunConfiguration setting in auto_prepare_environment.
    + Deprecated parameters `conda_dependencies_file_path` and `pip_requirements_file_path` in favor of `conda_dependencies_file` and `pip_requirements_file` respectively.
  + **azureml-opendatasets**
    + Improve NoaaIsdWeather enrich performance in non-SPARK version significantly.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **新功能**
 + Dataflow objects can now be iterated over, producing a sequence of records. See documentation for `Dataflow.to_record_iterator`.
  + Dataflow objects can now be iterated over, producing a sequence of records. See documentation for `Dataflow.to_record_iterator`.

+ **Bug fixes and improvements**
 + Increased the robustness of DataPrep SDK.
 + Improved handling of pandas DataFrames with non-string Column Indexes.
 + Improved the performance of `to_pandas_dataframe` in Datasets.
 + Fixed a bug where Spark execution of Datasets failed when run in a multi-node environment.
  + Increased the robustness of DataPrep SDK.
  + Improved handling of pandas DataFrames with non-string Column Indexes.
  + Improved the performance of `to_pandas_dataframe` in Datasets.
  + Fixed a bug where Spark execution of Datasets failed when run in a multi-node environment.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

We reverted a change that improved performance, as it was causing issues for some customers using Azure Databricks. If you experienced an issue on Azure Databricks, you can upgrade to version 1.1.7 using one of the methods below:
1. Run this script to upgrade: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recreate the cluster, which will install the latest Data Prep SDK version.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK for Python v1.0.45

+ **新功能**
  + Add decision tree surrogate model to mimic explainer in azureml-explain-model package
  + Ability to specify a CUDA version to be installed on Inferencing images. Support for CUDA 9.0, 9.1, and 10.0.
  + Information about Azure ML training base images are now available at [Azure ML Containers GitHub Repository](https://github.com/Azure/AzureML-Containers) and [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Added CLI support for pipeline schedule. Run "az ml pipeline -h" to learn more
  + Added custom Kubernetes namespace parameter to AKS webservice deployment configuration and CLI.
  + Deprecated hash_paths parameter for all pipeline steps
  + Model.register now supports registering multiple individual files as a single model with use of the `child_paths` parameter.
  
+ **預覽功能**
    + Scoring explainers can now optionally save conda and pip information for more reliable serialization and deserialization.
    + Bug Fix for Auto Feature Selector.
    + Updated mlflow.azureml.build_image to the new api, patched bugs exposed by the new implementation.

+ **Bug fixes and improvements**
  + Removed `paramiko` dependency from azureml-core. Added deprecation warnings for legacy compute target attach methods.
  + Improve performance of run.create_children
  + In mimic explainer with binary classifier, fix the order of probabilities when teacher probability is used for scaling shape values.
  + Improved error handling and message for Automated machine learning. 
  + Fixed the iteration timeout issue for Automated machine learning.
  + Improved the time-series transformation performance for Automated machine learning.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **新功能**
  + Added summary functions for top values (`SummaryFunction.TOPVALUES`) and bottom values (`SummaryFunction.BOTTOMVALUES`).

+ **Bug fixes and improvements**
  + Significantly improved the performance of `read_pandas_dataframe`.
  + Fixed a bug that would cause `get_profile()` on a Dataflow pointing to binary files to fail.
  + Exposed `set_diagnostics_collection()` to allow for programmatic enabling/disabling of the telemetry collection.
  + Changed the behavior of `get_profile()`. NaN values are now ignored for Min, Mean, Std, and Sum, which aligns with the behavior of Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK for Python v1.0.43

+ **新功能**
  + Azure Machine Learning now provides first-class support for popular machine learning and data analysis framework Scikit-learn. Using [`SKLearn` estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), users can easily train and deploy Scikit-learn models.
    + Learn how to [run hyperparameter tuning with Scikit-learn using HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Added support for creating ModuleStep in pipelines along with Module and ModuleVersion classes to manage reusable compute units.
  + ACI webservices now support persistent scoring_uri through updates. The scoring_uri will change from IP to FQDN. The Dns Name Label for FQDN can be configured by setting the dns_name_label on deploy_configuration. 
  + Automated machine learning new features:
    + STL featurizer for forecasting
    + KMeans clustering is enabled for feature sweeping
  + AmlCompute Quota approvals just became faster! We have now automated the process to approve your quota requests within a threshold. For more information on how quotas work, learn [how to manage quotas](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **預覽功能**
    + Integration with [MLflow](https://mlflow.org) 1.0.0 tracking through azureml-mlflow package ([example notebooks](https://aka.ms/azureml-mlflow-examples)).
    + Submit Jupyter notebook as a run. [API Reference Documentation](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Public Preview of [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) through azureml-contrib-datadrift package ([example notebooks](https://aka.ms/azureml-datadrift-example)). Data Drift is one of the top reasons where model accuracy degrades over time. It happens when data served to model in production is different from the data that the model was trained on. AML Data Drift detector helps customer to monitor data drift and sends alert whenever drift is detected. 

+ **重大變更**

+ **Bug fixes and improvements**
  + RunConfiguration load and save supports specifying a full file path with full back-compat for previous behavior.
  + Added caching in ServicePrincipalAuthentication, turned off by default.
  + Enable logging of multiple plots under the same metric name.
  + Model class now properly importable from azureml.core (`from azureml.core import Model`).
  + In pipeline steps, `hash_path` parameter is now deprecated. New behavior is to hash complete source_directory, except files listed in .amlignore or .gitignore.
  + In pipeline packages, various `get_all` and `get_all_*` methods have been deprecated in favor of `list` and `list_*`, respectively.
  + azureml.core.get_run no longer requires classes to be imported before returning the original run type.
  + Fixed an issue where some calls to WebService Update did not trigger an update.
  + Scoring timeout on AKS webservices should be between 5ms and 300000ms. Max allowed scoring_timeout_ms for scoring requests has been bumped from 1 min to 5 min.
  + LocalWebservice objects now have `scoring_uri` and `swagger_uri` properties.
  + Moved outputs directory creation and outputs directory upload out of the user process. Enabled run history SDK to run in every user process. This should resolve some synchronization issues experienced by distributed training runs.
  + The name of the azureml log written from the user process name will now include process name (for distributed training only) and PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Bug fixes and improvements**
  + For interpreted datetime values that have a 2-digit year format, the range of valid years has been updated to match Windows May Release. The range has been changed from 1930-2029 to 1950-2049.
  + When reading in a file and setting `handleQuotedLineBreaks=True`, `\r` will be treated as a new line.
  + Fixed a bug that caused `read_pandas_dataframe` to fail in some cases.
  + Improved performance of `get_profile`.
  + Improved error messages.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **新功能**
  + You can now use the following expression language functions to extract and parse datetime values into new columns.
    + `RegEx.extract_record()` extracts datetime elements into a new column.
    + `create_datetime()` creates datetime objects from separate datetime elements.
  + When calling `get_profile()`, you can now see that quantile columns are labeled as (est.) to clearly indicate that the values are approximations.
  + You can now use ** globbing when reading from Azure Blob Storage.
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **錯誤修正**
  + Fixed a bug related to reading a Parquet file from a remote source (Azure Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK for Python v1.0.39
+ **變更**
  + Run configuration auto_prepare_environment option is being deprecated, with auto prepare becoming the default.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **新功能**
  + Added support to read from a PostgresSQL database, either by calling read_postgresql or using a Datastore.
    + See examples in how-to guides:
      + [Data Ingestion notebook](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Datastore notebook](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug fixes and improvements**
  + Fixed issues with column type conversion:
  + Now correctly converts a boolean or numeric column to a boolean column.
  + Now does not fail when attempting to set a date column to be date type.
  + Improved JoinType types and accompanying reference documentation. When joining two dataflows, you can now specify one of these types of join:
    + NONE, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Improved data type inferencing to recognize more date formats.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

In Azure portal, you can now:
+ Create and run automated ML experiments 
+ Create a Notebook VM to try out sample Jupyter notebooks or your own.
+ Brand new Authoring section (Preview) in the Machine Learning service workspace, which includes Automated Machine Learning, Visual Interface and Hosted Notebook VMs
    + Automatically create a model using Automated machine learning 
    + Use a drag and drop Visual Interface to run experiments
    + Create a Notebook VM to explore data, create models, and deploy services.
+ Live chart and metric updating in run reports and run details pages
+ Updated file viewer for logs, outputs, and snapshots in Run details pages.
+ New and improved report creation experience in the Experiments tab. 
+ Added ability to download the config.json file from the Overview page of the Azure Machine Learning workspace.
+ Support Machine Learning service workspace creation from Azure Databricks workspace 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK for Python v1.0.33
+ **新功能**
  + The _Workspace.create_ method now accepts default cluster configurations for CPU and GPU clusters.
  + If Workspace creation fails, depended resources are cleaned.
  + Default Azure Container Registry SKU was switched to basic.
  + Azure Container Registry is created lazily, when needed for run or image creation.
  + Support for Environments for training runs.

### <a name="notebook-virtual-machine"></a>Notebook Virtual Machine 

Use a Notebook VM as a secure, enterprise-ready hosting environment for Jupyter notebooks in which you can program machine learning experiments, deploy models as web endpoints and perform all other operations supported by Azure Machine Learning SDK using Python. It provides several capabilities:
+ [Quickly spin up a preconfigured notebook VM](tutorial-1st-experiment-sdk-setup.md) that has the latest version of Azure Machine Learning SDK and related packages.
+ Access is secured through proven technologies, such as HTTPS, Azure Active Directory authentication and authorization.
+ Reliable cloud storage of notebooks and code in your Azure Machine Learning Workspace blob storage account. You can safely delete your notebook VM without losing your work.
+ Preinstalled sample notebooks to explore and experiment with Azure Machine Learning features.
+ Full customization capabilities of Azure VMs, any VM type, any packages, any drivers. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v1.0.33 released.

+ Azure ML Hardware Accelerated Models on [FPGAs](concept-accelerate-with-fpgas.md) is generally available.
  + You can now [use the azureml-accel-models package](how-to-deploy-fpga-web-service.md) to:
    + Train the weights of a supported deep neural network (ResNet 50, ResNet 152, DenseNet-121, VGG-16, and SSD-VGG)
    + Use transfer learning with the supported DNN
    + Register the model with Model Management Service and containerize the model
    + Deploy the model to an Azure VM with an FPGA in an Azure Kubernetes Service (AKS) cluster
  + Deploy the container to an [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server device
  + Score your data with the gRPC endpoint with this [sample](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ Feature sweeping to enable dynamically adding :::no-loc text="featurizers"::: for performance optimization. New :::no-loc text="featurizers":::: work embeddings, weight of evidence, target encodings, text target encoding, cluster distance
+ Smart CV to handle train/valid splits inside automated ML
+ Few memory optimization changes and runtime performance improvement
+ Performance improvement in model explanation
+ ONNX model conversion for local run
+ Added Subsampling support
+ Intelligent Stopping when no exit criteria defined
+ Stacked ensembles

+ Time Series Forecasting
  + New predict forecast function   
  + You can now use rolling-origin cross validation on time series data
  + New functionality added to configure time series lags 
  + New functionality added to support rolling window aggregate features
  + New Holiday detection and featurizer when country code is defined in experiment settings

+ Azure Databricks
  + Enabled time series forecasting and model explainabilty/interpretability capability
  + You can now cancel and resume (continue) automated ML experiments
  + Added support for multicore processing

### <a name="mlops"></a>MLOps
+ **Local deployment & debugging for scoring containers**<br/> You can now deploy an ML model locally and iterate quickly on your scoring file and  dependencies to ensure they behave as expected.

+ **Introduced InferenceConfig & Model.deploy()**<br/> Model deployment now supports specifying a source folder with an entry script, the same as a RunConfig.  Additionally, model deployment has been simplified to a single command.

+ **Git reference tracking**<br/> Customers have been requesting basic Git integration capabilities for some time as it helps maintain an end-to-end audit trail. We have implemented tracking across major entities in Azure ML for Git-related metadata (repo, commit, clean state). This information will be collected automatically by the SDK and CLI.

+ **Model profiling & validation service**<br/> Customers frequently complain of the difficulty to properly size the compute associated with their inference service. With our model profiling service, the customer can provide sample inputs and we will profile across 16 different CPU / memory configurations to determine optimal sizing for deployment.

+ **Bring your own base image for inference**<br/> Another common complaint was the difficulty in moving from experimentation to inference RE sharing dependencies. With our new base image sharing capability, you can now reuse your experimentation base images, dependencies and all, for inference. This should speed up deployments and reduce the gap from the inner to the outer loop.

+ **Improved Swagger schema generation experience**<br/> Our previous swagger generation method was error prone and impossible to automate. We have a new in-line way of generating swagger schemas from any Python function via decorators. We have open-sourced this code and our schema generation protocol is not coupled to the Azure ML platform.

+ **Azure ML CLI is generally available (GA)**<br/> Models can now be deployed with a single CLI command. We got common customer feedback that no one deploys an ML model from a Jupyter notebook. The [**CLI reference documentation**](https://aka.ms/azmlcli) has been updated.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v1.0.30 released.

The [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) was introduce to add a new version of a published pipeline while maintaining same endpoint.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Note: Data Prep Python SDK will no longer install `numpy` and `pandas` packages. See [updated installation instructions](https://aka.ms/aml-data-prep-installation).

+ **新功能**
  + You can now use the Pivot transform.
    + How-to guide: [Pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + You can now use regular expressions in native functions.
    + 範例：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + You can now use `to_upper` and `to_lower` functions in expression language.
  + You can now see the number of unique values of each column in a data profile.
  + For some of the commonly used reader steps, you can now pass in the `infer_column_types` argument. If it is set to `True`, Data Prep will attempt to detect and automatically convert column types.
    + `inference_arguments` is now deprecated.
  + You can now call `Dataflow.shape`.

+ **Bug fixes and improvements**
  + `keep_columns` now accepts an additional optional argument `validate_column_exists`, which checks if the result of `keep_columns` will contain any columns.
  + All reader steps (which read from a file) now accept an additional optional argument `verify_exists`.
  + Improved performance of reading from pandas dataframe and getting data profiles.
  + Fixed a bug where slicing a single step from a Dataflow failed with a single index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + You can now resubmit an existing Script run on an existing remote compute cluster. 
  + You can now run a published pipeline with new parameters on the Pipelines tab. 
  + Run details now supports a new Snapshot file viewer. You can view a snapshot of the directory when you submitted a specific run. You can also download the notebook that was submitted to start the run.
  + You can now cancel parent runs from the Azure portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v1.0.23

+ **新功能**
  + The Azure Machine Learning SDK now supports Python 3.7.
  + Azure Machine Learning DNN Estimators now provide built-in multi-version support. For example, `TensorFlow` estimator now accepts a `framework_version` parameter, and users can specify version '1.10' or '1.12'. For a list of the versions supported by your current SDK release, call `get_supported_versions()` on the desired framework class (for example, `TensorFlow.get_supported_versions()`).
  For a list of the versions supported by the latest SDK release, see the [DNN Estimator documentation](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **新功能**
  + You can read multiple Datastore/DataPath/DataReference sources using read_* transforms.
  + You can perform the following operations on columns to create a new column: division, floor, modulo, power, length.
  + Data Prep is now part of the Azure ML diagnostics suite and will log diagnostic information by default.
    + To turn this off, set this environment variable to true: DISABLE_DPREP_LOGGER

+ **Bug fixes and improvements**
  + Improved code documentation for commonly used classes and functions.
  + Fixed a bug in auto_read_file that failed to read Excel files.
  + Added option to overwrite the folder in read_pandas_dataframe.
  + Improved performance of dotnetcore2 dependency installation, and added support for Fedora 27/28 and Ubuntu 1804.
  + Improved the performance of reading from Azure Blobs.
  + Column type detection now supports columns of type Long.
  + Fixed a bug where some date values were being displayed as timestamps instead of Python datetime objects.
  + Fixed a bug where some type counts were being displayed as doubles instead of integers.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v1.0.21

+ **新功能**
  + The *azureml.core.Run.create_children* method allows low-latency creation of multiple child runs with a single call.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **重大變更**
  + The concept of the Data Prep Package has been deprecated and is no longer supported. Instead of persisting multiple Dataflows in one Package, you can persist Dataflows individually.
    + How-to guide: [Opening and Saving Dataflows notebook](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + Data Prep can now recognize columns that match a particular Semantic Type, and split accordingly. The STypes currently supported include: email address, geographic coordinates (latitude & longitude), IPv4 and IPv6 addresses, US phone number, and US zip code.
    + How-to guide: [Semantic Types notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep now supports the following operations to generate a resultant column from two numeric columns: subtract, multiply, divide, and modulo.
  + You can call `verify_has_data()` on a Dataflow to check whether the Dataflow would produce records if executed.

+ **Bug fixes and improvements**
  + You can now specify the number of bins to use in a histogram for numeric column profiles.
  + The `read_pandas_dataframe` transform now requires the DataFrame to have string- or byte- typed column names.
  + Fixed a bug in the `fill_nulls` transform, where values were not correctly filled in if the column was missing.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **變更**
   + The azureml-tensorboard package replaces azureml-contrib-tensorboard.
   + With this release, you can set up a user account on your managed compute cluster (amlcompute), while creating it. This can be done by passing these properties in the provisioning configuration. You can find more details in the [SDK reference documentation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **新功能**
  + Now supports adding two numeric columns to generate a resultant column using the expression language.

+ **Bug fixes and improvements**
  + Improved the documentation and parameter checking for random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Bug fix**
  + Fixed a Service Principal authentication issue that was caused by an API change.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **新功能**

  + Azure Machine Learning now provides first class support for popular DNN framework Chainer. Using [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) class users can easily train and deploy Chainer models.
    + Learn how to [run distributed training with ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Learn how to [run hyperparameter tuning with Chainer using HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines added ability trigger a Pipeline run based on datastore modifications. The pipeline [schedule notebook](https://aka.ms/pl-schedule) is updated to showcase this feature.

+ **Bug fixes and improvements**
  + We have added support Azure Machine Learning Pipelines for setting the source_directory_data_store property to a desired datastore (such as a blob storage) on [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) that are supplied to the [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). By default Steps use Azure File store as the backing datastore, which may run into throttling issues when a large number of steps are executed concurrently.

### <a name="azure-portal"></a>Azure Portal

+ **新功能**
  + New drag and drop table editor experience for reports. Users can drag a column from the well to the table area where a preview of the table will be displayed. The columns can be rearranged.
  + New Logs file viewer
  + Links to experiment runs, compute, models, images, and deployments from the activities tab

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **新功能**
  + Data Prep now supports writing file streams from a dataflow. Also provides the ability to manipulate the file stream names to create new file names.
    + How-to guide: [Working With File Streams notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug fixes and improvements**
  + Improved performance of t-Digest on large data sets.
  + Data Prep now supports reading data from a DataPath.
  + One hot encoding now works on boolean and numeric columns.
  + Other miscellaneous bug fixes.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>適用於 Python 的 Azure Machine Learning SDK v1.0.15

+ **新功能**
  + Azure Machine Learning Pipelines added AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook), and time-based scheduling functionality ([notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep 已更新成可與 Azure SQL Server 和「適用於 PostgreSQL 的 Azure 資料庫」([otebook](https://aka.ms/pl-data-trans)) 搭配運作。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 「資料準備」現在支援使用「資料存放區」從 Azure SQL 資料庫讀取資料。
 
+ **變更**
  + Improved the memory performance of certain operations on large data.
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**： 
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
    + If you are performing "az login" and then using azureml-sdk, the SDK will do the browser or device code log in one more time. 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您正在使用服務主體執行「az login」以進行自動化，建議您使用 _azureml.core.authentication.ServicePrincipalAuthentication_ 類別，因為 azureml-sdk 不會使用 azure CLI 建立的認證狀態。 

+ **Bug fixes**: This release mostly contains minor bug fixes

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **錯誤修正**
  + Improved the performance of getting data profiles.
  + 已修正與錯誤報告相關的輕微錯誤。
  
### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 全新的拖放圖表報告製作體驗。 使用者可以將資料行或屬性從 well 拖曳至圖表區域，在此區域系統將根據資料類型自動為使用者選取適當的圖表類型。 使用者可以將圖表類型變更為其他適用的類型，或新增其他屬性。

    支援的圖表類型：
    - 折線圖
    - 長條圖
    - 堆疊橫條圖
    - 盒狀圖
    - 散佈圖
    - 泡泡圖
+ 入口網站現在會動態地產生實驗報告。 當使用者將回合提交給實驗後，具有計量和圖表記錄的報告就會自動產生，以便您比較每個不同的回合。 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK for Python v1.0.8

+ **Bug fixes**: This release mostly contains minor bug fixes

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **新功能**
  + 資料存放區改善 (記載於[資料存放區操作指南](https://aka.ms/aml-data-prep-datastore-nb))
    + 已新增以相應增加方式從中讀取和寫入至 Azure 檔案共用和 ADLS 資料存放區的功能。
    + 使用資料存放區時，Data Prep 現在支援使用服務主體驗證，而非互動式驗證。
    + 已新增 wasb 和 wasbs url 的支援。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning 資料準備 SDK v1.0.6

+ **錯誤修正**
  + 已修正與從 Spark 上公開可讀取的 Azure Blob 容器讀取相關的 Bug

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **Bug fixes**: This release mostly contains minor bug fixes

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **新功能**
  + `to_bool` 函式現在允許將不相符的值轉換成 Error 值。 這是 `to_bool` 和 `set_column_types` 新的預設不相符行為，先前的預設行為會將不相符的值轉換為 False。
  + 在呼叫 `to_pandas_dataframe` 時，有新的選項可將數字資料行中的 null/遺失值解譯為 NaN。
  + 新增了功能，會檢查某些運算式的傳回類型，以確保類型一致性，並及早失敗。
  + 您現在可以呼叫 `parse_json`，以將資料行中的值剖析為 JSON 物件，並將其展開成多個資料行。

+ **錯誤修正**
  + 已修正錯誤，該錯誤會讓 Python 3.5.2 中的 `set_column_types` 損毀。
  + 已修正會在使用 AML 映像連線至資料存放區時當機的錯誤。

+ **更新**
  * 使用者入門教學課程、案例研究和操作指南的[範例 Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04: General Availability

Azure Machine Learning is now generally available.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
在此版本中，我們透過 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 來宣布新的受控計算體驗。 這個計算目標會取代適用於 Azure Machine Learning 的 Azure Batch AI 計算。 

這個計算目標：
+ Is used for model training and batch inference/scoring
+ 是單一對多個節點的計算
+ 會為使用者執行叢集管理和作業排程
+ 會依預設自動調整
+ 同時支援 CPU 和 GPU 資源 
+ 可讓您使用低優先順序的 VM 來降低成本

您可以在 Python 中，使用 Azure 入口網站或 CLI 建立 Azure Machine Learning Compute。 它必須建立於您工作區所在的區域，而且無法附加至任何其他工作區。 這個計算目標會針對您的回合使用 Docker 容器，並封裝您的相依性，以便在您的所有節點上複寫相同的環境。

> [!Warning]
> 我們建議您建立新的工作區來使用 Azure Machine Learning Compute。 使用者嘗試從現有的工作區建立 Azure Machine Learning Compute 可能會看到錯誤，但機率很低。 您工作區中現有的計算應能持續運作而不會受到影響。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **重大變更**
  + 在此版本中，我們會移除從 Azure Machine Learning 中建立 VM 的支援。 您仍然可以附加現有的雲端 VM 或遠端內部部署伺服器。 
  + 我們也會移除對 BatchAI 的支援，這些功能現在全都應透過 Azure Machine Learning Compute 來支援。

+ **新增**
  + 針對機器學習管線：
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py) \(英文\)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py) \(英文\)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py) \(英文\)


+ **已更新**
  + 針對機器學習管線：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) \(英文\) 現在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) \(英文\) 現在可從 SQL 資料來源複製或複製到其中
    + 排程 SDK 中的功能，以建立和更新執行已發佈管線的排程

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning 資料準備 SDK v0.5.2
+ **重大變更** 
  * `SummaryFunction.N` 已重新命名為 `SummaryFunction.Count`。
  
+ **錯誤修正**
  * 讀取自和寫入至遠端回合上的資料存放區時，請使用最新 AML 執行權杖 以前，如果在 Python 中更新 AML 執行權杖，將不會使用已更新的 AML 執行權杖來更新資料準備執行階段。
  * 其他更清楚的錯誤訊息
  * 當 Spark 使用 `Kryo` 序列化時，to_spark_dataframe() 將不再損毀
  * 值計數偵測器現在可以顯示 1000 個以上的唯一值
  * 如果原始的資料流程沒有名稱，隨機分割就不再失敗  

+ **詳細資訊**
  * [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文件和 Notebook
+ ML 管線
  + 適用於開始使用管線、批次範圍和樣式移轉範例之全新和更新的 Notebook： https://aka.ms/aml-pipeline-notebooks
  + 了解如何[建立您的第一個管線](how-to-create-your-first-pipeline.md)
  + 了解如何[使用管線執行批次預測](how-to-run-batch-predictions.md)
+ Azure Machine Learning 計算目標
  + [範例 Notebook](https://aka.ms/aml-notebooks) 現在已更新為使用新的受控計算。
  + [了解這個計算](how-to-set-up-training-targets.md#amlcompute)。

### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 在入口網站中建立和管理 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 類型。
+ 監視 Azure Machine Learning Compute 的配額使用量和[要求配額](how-to-manage-quotas.md)。
+ 即時檢視 Azure Machine Learning Compute 叢集狀態。
+ 已新增對於建立 Azure Machine Learning Compute 和 Azure Kubernetes Service 的虛擬網路支援。
+ 使用現有的參數，重新執行您已發佈的管線。
+ 新的[自動化機器學習圖表](how-to-understand-automated-ml.md)，適用於分類模型 (升力、增益、校正、具備模型說明能力的特徵重要性圖表) 和迴歸模型 (殘差，以及具備模型說明能力的特徵重要性圖表)。 
+ 您可以在 Azure 入口網站中檢視管線。




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大變更** 
  * azureml.train.widgets 命名空間已移至 azureml.widgets。
  * azureml.core.compute.AmlCompute 取代了下列類別 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

若要深入了解 Data Prep SDK，請閱讀[參考文件](https://aka.ms/data-prep-sdk)。
+ **新功能**
   * 建立新的 DataPrep CLI 來執行 DataPrep 套件，並檢視資料集或資料流程的資料設定檔
   * 重新設計 SetColumnType API 以改善可用性
   * 將 smart_read_file 重新命名為 auto_read_file
   * 資料設定檔現在包含偏度和峰度
   * 可透過分層取樣來取樣
   * 可從包含 CSV 檔案的 zip 檔案讀取
   * 可使用隨機劃分來分割資料列取向的資料集 (例如，測試-訓練集)
   * 可以藉由呼叫 `.dtypes` 從資料流程或資料設定檔取得所有資料行的資料類型
   * 可以藉由呼叫 `.row_count` 從資料流程或資料設定檔取得資料列計數

+ **錯誤修正**
   * 修正長時間的雙重轉換 
   * 修正新增任何資料行之後的判斷提示 
   * 修正 FuzzyGrouping 在某些情況下無法偵測到群組的問題
   * 修正排序函式以遵循多欄排列順序
   * 將 and/or 運算式修正為類似於 `pandas` 處理它們的方式
   * 修正從 dbfs 路徑讀取的方式
   * 讓錯誤訊息變得更容易了解 
   * 現在使用 AML 權杖讀取遠端計算目標時不會再失敗
   * Linux DSVM 現在不會再有失敗狀況
   * 現在字串述詞中有非字串值時不會再發生損毀
   * 現在能在資料流程發生正常的失敗時，處理判斷提示錯誤
   * 現在支援 Azure Databricks 上掛接 dbutils 的儲存位置

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal 
The Azure portal for Azure Machine Learning has the following updates:
  * 用於已發佈管線的新 [管線] 索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更** 
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets() 取代為 Workspace.compute_targets。
  * *Run.get_context* 取代 *Run.get_submitted_run*。 第二個方法將會在後續版本中移除。
  * *PipelineData* 類別現在預期將 datastore 物件而不是 datastore_name 當作參數。 同樣地，*管線* 接受 default_datastore，而不是 default_datastore_name。

+ **新功能**
  * Azure Machine Learning 管線[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) 現在使用 MPI 步驟。
  * Jupyter Notebooks 的 RunDetails 小工具已更新以顯示管線的視覺效果。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **新功能**
  * 類型計數已新增至資料設定檔 
  * 值計數與長條圖現在可以使用
  * 資料設定檔中有更多百分位數
  * 中間值可以在 Summarize 中使用
  * 現在支援 Python 3.7
  * 當您將包含資料存放區的資料流程儲存至 DataPrep 套件時，資料存放區資訊將保存為 DataPrep 套件的一部分
  * 現在支援寫入至資料存放區 
        
+ **已修正的 Bug**
  * 64 位元不帶正負號的整數溢位現在會在 Linux 上正確處理
  * 已在 smart_read 中修正純文字檔案不正確的文字標籤
  * 字串資料行類型現在會出現在 [計量] 檢視中
  * 類型計數現在已修正，可顯示對應至單一 FieldType，而不是個別項目的 ValueKinds
  * 當路徑以字串方式提供時，Write_to_csv 不會再失敗
  * 使用 Replace 時，將 “find” 留空將不會再失敗 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新功能**
  * 建立新工作區時的多個租用戶支援。

+ **已修正的 BUG**
  * 在部署 Web 服務時，您不再需要釘選 pynacl 程式庫版本。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning 資料準備 SDK v0.3.0

+ **新功能**
  * 已新增 transform_partition_with_file(script_path) 方法，可讓使用者傳入要執行的 Python 檔案路徑

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[版本 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) 包含新功能、更多文件、錯誤修正和更多 [Notebook 範例](https://aka.ms/aml-notebooks)。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

+ **重大變更**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images、Workspace.web_services 會傳回字典，先前傳回清單。 請參閱 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 文件。

  * 自動化 Machine Learning 已將正規化均方誤差從主要計量中移除。

+ **HyperDrive**
  * 各種貝氏的 HyperDrive bug 修正，以及取得計量呼叫的效能提升。 
  * Tensorflow 從 1.9 升級至 1.10 
  * 針對冷啟動的 Docker 映像最佳化。 
  * 作業現在會報告正確的狀態，即使是以非 0 的錯誤碼結束。 
  * SDK 中的 RunConfig 屬性驗證。 
  * HyperDrive 執行物件支援類似於一般執行的取消作業：不需要傳遞任何參數。 
  * 改善小工具以維護分散式執行和 HyperDrive 執行的下拉式清單值狀態。 
  * 已修正參數伺服器的 TensorBoard 和其他記錄檔支援。 
  * 服務端上的 Intel(R) MPI 支援。 
  * 修正 BatchAI 驗證期間散發執行修正的參數微調錯誤。 
  * 內容管理員現在會識別主要執行個體。 

+ **Azure 入口網站體驗**
  * 執行詳細資料中支援 log_table() 和 log_row()。 
  * 自動建立有 1、2 或 3 個數值資料行和選擇性類別資料行的資料表和資料列圖形。

+ **自動化 Machine Learning**
  * 改良錯誤處理和文件 
  * 已修正 run 屬性擷取的效能問題。 
  * 已修正繼續執行的問題。 
  * Fixed :::no-loc text="ensembling"::: iteration issues.
  * 已修正 MAC OS 上的訓練懸置。
  * 對自訂驗證案例中的巨集平均 PR/ROC 曲線縮小取樣。
  * 已移除額外的索引邏輯。
  * 已從 get_output API 中移除篩選條件。

+ **管線**
  * 已新增 Pipeline.publish() 方法來直接發佈管線，而不需要先啟動執行。   
  * 已新增 PipelineRun.get_pipeline_runs() 方法來擷取從所發佈管線產生的管線執行。

+ **Project Brainwave**
  * 已更新 FPGA 上可用的新 AI 模型支援。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning 資料準備 SDK v0.2.0
[版本 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) 包含下列功能和錯誤修正：

+ **新功能**
  * One-hot 編碼的支援
  * 分量轉換的支援
   
+ **已修正的 Bug：**
  * 適用於任何 Tornado 版本，不必降級 Tornado 版本
  * 值計數代表所有值，不只是前三個值

## <a name="2018-09-public-preview-refresh"></a>2018-09 (公開預覽刷新版)

A new, refreshed release of Azure Machine Learning: Read more about this release: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning](../service/overview-what-is-azure-ml.md) 的概觀。
