### Building ML Pipelines with Kubeflow

https://github.com/https-deeplearning-ai/machine-learning-engineering-for-production-public/blob/main/course4/week3-ungraded-labs/C4_W3_Lab_1_Intro_to_KFP/C4_W3_Lab_1_Kubeflow_Pipelines.ipynb

- Use the "component" decorator to specify that a Python function is a Kubeflow Pipeline component.
- Use the "dsl.pipeline" decorator to specify that a sequence of components constitute a pipeline.

From https://aicurious.io/blog/2022-03-26-airflow-mlflow-or-kubeflow-for-mlops and https://www.linkedin.com/pulse/airflow-vs-kubeflow-mlflow-whats-difference-jack-molloy/

- Airflow is used in many other tasks which need workflow orchestration (instead targeting only ML like Kubeflow or MLFlow).
- Airflow(workflow orchestration) `+` MLFlow(experiment tracking, model management) `=` Kubeflow(workflow orchestration, experiment tracking, model management)

