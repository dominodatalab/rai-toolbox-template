# rai-toolbox-template
Responsible AI Toolbox Project Template

## Description

This template demonstrates how to use Microsoft's [Responsible AI Toolbox](https://responsibleaitoolbox.ai/) suite of tools in a Domino workspace.

It covers:
- How to create a Domino compute environment for running the Responsible AI Toolbox.
- Sample code for computing data that can be ingested into the dashboard.
- Code snippets for creating a link to open the dashboard inside of a Domino workspace.

To learn more about the tools included in the Responsible AI Toolbox and how to use them to analyze models and dataset, you can reference these resources:
- Microsoft's [responsibleaitoolbox.ai](https://responsibleaitoolbox.ai/) website.
- The [responsible-ai-toolbox](https://github.com/microsoft/responsible-ai-toolbox) GitHub project. In particular:
  - [Tour](https://github.com/microsoft/responsible-ai-toolbox/blob/main/notebooks/responsibleaidashboard/tabular/tour.ipynb).
  - [Getting Started](https://github.com/microsoft/responsible-ai-toolbox/blob/main/notebooks/responsibleaidashboard/tabular/getting-started.ipynb).
  - [Notebook examples](https://github.com/microsoft/responsible-ai-toolbox/tree/main/notebooks).

## Environment

To use the Responsible AI Toolbox, the `raiwidgets` Python package must be installed. In addition to that, depending on which tools you want to use, you may need to install additional packages. See the [responsible-ai-toolbox](https://github.com/microsoft/responsible-ai-toolbox) documentation for details.

To create a Domino compute environment that meets the requirements:

1. On the `Environments` page, click on `Create Environment`.
2. On the `Name` field enter the environment name (e.g. "raiwidgets").
3. On the `Base Environment / Image` group, select "Start from a custom base image" and enter this image URI on the `FROM` field:
```
quay.io/domino/compute-environment-images:ubuntu20-py3.9-domino5.8-minimal
```
4. On the `Visibility` field, choose between "Private" (the default) or "Globally Accessible".
5. Click the `Customize before building` button.
6. On the `Dockerfile Instructions` field, enter:
```dockerfile
# Install the required package for displaying the dashboard
USER root
RUN pip install raiwidgets==0.33.0
# Install additional, optional packages as required
RUN pip install responsibleai-text==0.2.5
RUN pip install responsibleai-vision==0.3.5
# Set the default user
USER ubuntu
```
7. On the `Pluggable Workspace Tools` field, enter:
```yaml
jupyterlab:
  title: "JupyterLab"
  iconUrl: "/assets/images/workspace-logos/jupyterlab.svg"
  start: [  "/opt/domino/workspaces/jupyterlab/start" ]
  httpProxy:
    internalPath: "/{{ownerUsername}}/{{projectName}}/{{sessionPathComponent}}/{{runId}}/{{#if pathToOpen}}tree/{{pathToOpen}}{{/if}}"
    port: 8888
    rewrite: false
    requireSubdomain: false
```
8. At the bottom of the page, click on `Build`.
9. Wait for the build status to become a green checkmark ("Succeeded"). Click on the `Build Logs` link in case of an error.

## Workspace

To create a JupyterLab workspace to run the dashboard:

1. Open the project and navigate to its `Workspaces` page.
2. Click on `Create New Workspace`.
3. On the `Workspace Environment` field, select the compute environment created above (e.g. "raiwidgets").
4. Select the `JupyterLab` IDE.
5. Click the `Launch` button.

After the JupyterLab interface opens, browse the `examples` folder and run the provided notebook.

At the bottom of the notebook, after it finishes running, there will be a link to open the Responsible AI Dashboard.

## Dashboard link

The Responsible AI Dashboard runs as a Flask app on the same container that is running the Domino workspace. It will listen on a port in the range 8704 to 8993.

In order to be able to access the dashboard, the appropriate link must be assembled. Jupyter and JupyterLab have the ability to reverse proxy requests by appending the `/proxy/{port}` path to the URL.

This snippet will display a clickable link that will open the dashboard on a new browser tab, assuming that the `dashboard` Python variable contains the Dashboard object:

```python
import os
from IPython.display import HTML
DOMINO_USER_NAME = os.environ.get('DOMINO_USER_NAME')
DOMINO_PROJECT_NAME = os.environ.get('DOMINO_PROJECT_NAME')
DOMINO_RUN_ID = os.environ.get('DOMINO_RUN_ID')
port=dashboard.config['baseUrl'].split(':')[-1]
url = f"/{DOMINO_USER_NAME}/{DOMINO_PROJECT_NAME}/notebookSession/{DOMINO_RUN_ID}/proxy/{port}"
display(HTML(f'<a href="{url}" target="_blank">Responsible AI Dashboard</a>'))
```

See [examples/responsibleaidashboard-housing-decision-making.ipynb](examples/responsibleaidashboard-housing-decision-making.ipynb) for a complete example.
