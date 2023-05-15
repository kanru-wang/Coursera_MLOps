### Customize TFX Components

https://github.com/https-deeplearning-ai/machine-learning-engineering-for-production-public/blob/main/course4/week3-ungraded-labs/C4_W3_Lab_2_TFX_Custom_Components.ipynb

- Build a custom component as a Python function-based component with a "@component" decorator
- Build a custom component by modifying three existing TFX components:
  - Component Spec - this describes the inputs, outputs, and parameters passed on to the component
  - Executor - code for processing the inputs, outputs, and parameters
  - Component Class - puts everything together so your code can be run by the TFX runner
- Run a TFX pipeline locally using a built-in pipeline orchestrator

As a digression, TensorFlow Data Validation (TFDV) of TFX can:
- Calculate descriptive statistics on two datasets, and visualize the differences
- Infer data schema (e.g. data types, expected value range) for validating new data. The schema can be manually revised
