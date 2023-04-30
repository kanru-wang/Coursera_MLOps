### GitHub Actions

#### Unit test example

```yml
name: Unit test example

# Controls when the action will run
on:
  # Triggers the workflow on push request events only when there are changes in the desired path
  push:
    paths:
      - 'my_directory/**'

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "test"
  test:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    defaults:
      run:
        # Use bash as the shell
        shell: bash
        # Specify the working directory for the workflow
        working-directory: my_directory/

    steps:
      -
        # Usually included in every Action since it allows GitHub to check-out the repo
        name: Checkout
        uses: actions/checkout@v2
      - 
        # Specify the desired Python version
        name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.7.7'
      - 
        name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install numpy fastapi uvicorn scikit-learn pytest
      -
        name: Test with pytest
        run: |
          pytest
```

#### Unit test and linting example
From: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-python

```yml
name: Python package

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.7", "3.8", "3.9", "3.10", "3.11"]

    steps:
      - uses: actions/checkout@v3
      - name: Setup Python # Set Python version
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
       - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install ruff pytest
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
      - name: Lint with ruff
        run: |
          # stop the build if there are Python syntax errors or undefined names
          ruff --format=github --select=E9,F63,F7,F82 --target-version=py37 .
          # default set of ruff rules with GitHub Annotations
          ruff --format=github --target-version=py37 .
      - name: Test with pytest
        run: pytest tests.py --doctest-modules --junitxml=junit/test-results-${{ matrix.python-version }}.xml
      - name: Upload pytest test results
        uses: actions/upload-artifact@v3
        with:
          name: pytest-results-${{ matrix.python-version }}
          path: junit/test-results-${{ matrix.python-version }}.xml
        # Use always() to always run this step to publish test results when there are test failures
        if: ${{ always() }}
```

#### Build and publish example
From: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-python

```yml
name: Upload Python Package

on:
  release:
    types: [published]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install build
      - name: Build package
        run: python -m build
      - name: Publish package
        uses: pypa/gh-action-pypi-publish@27b31702a0e7fc50959f5ad993c78deac1bdfc29
        with:
          user: __token__
          password: ${{ secrets.PYPI_API_TOKEN }}
```
