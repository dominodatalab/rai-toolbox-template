# rai-toolbox-template
Responsible AI Toolbox Project Template

## Environment

To use the Responsible AI Toolbox, the `raiwidgets` Python package must be installed.

To create a Domino compute environment that meets the requirements:

1. On the `Environments` page, click on `Create Environment`.

2. On the `Environment Base` group, select "Start from a custom base image" and enter this image on the `FROM` field:

```
quay.io/domino/compute-environment-images:ubuntu20-py3.9-domino5.8-minimal
```

3. On the `Dockerfile Instructions` field, enter:

```
USER root
RUN pip install raiwidgets==0.33.0
USER ubuntu
```

4. On the `Pluggable Workspace Tools` field, enter:

```
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

5. At the bottom of the page, click on `Build`.

## Workspace

To create a JupyterLab workspace to run the dashboard:

1. Open the project and navigate to its `Workspaces` page.
2. Click on `Create New Workspace`.
3. On the `Workspace Environment` field, select the compute environment created above (e.g. "raiwidgets").
4. Select the `JupyterLab` IDE.
5. Click the `Launch` button.

After the JupyterLab interface opens, browse the `examples` folder and run the provided notebook.

At the bottom of the notebook, after it finishes running, there will be a link to open the Responsible AI Dashboard.
