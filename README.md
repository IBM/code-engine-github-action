# Code Engine GitHub Action

This GitHub Action allows you to interact with IBM Cloud Code Engine. Deploy Apps, Jobs, and Functions or build Container images. It offers flexibility for different deployment types and provides various configuration options.

## Inputs

| Name             | Required | Default Value    |Description                                                                                                                                                                                                                                                         |
|------------------|----|-----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `api-key`        | ✅ | -                           | IAM API Key used to log into the IBM Cloud. Please store your IBM Cloud API key securely in your GitHub repository Secrets. |
| `resource-group` | ❌ | Your Default Resource Group | An IBM Cloud Resource Group, a logical container for organizing and managing related cloud resources.|
| `region`         | ✅ | -                           | The geographical area where your Code Engine project is located, like `eu-de` [codeengine-regions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-regions)|
| `project`        | ✅ | -                           | The unique identifier (GUID) or the name that identifies your IBM Cloud Code Engine project. |
| `component`      | ✅ | -                           | The type of component to deploy. allowed values `application`, `app`, `function`, `func`, `fn`, `job`, `build` |
| `name`           | ✅ | -                           | The name of the App, Function, or Job. |
| `image`          | ❌ | - | Name of the image used for this application or job |
| `registry-secret`| ❌ | - | Name of the Code Engine registry secret used to authenticate against the Container registry |
| `build-source`   | ❌ | .                           | Path to the directory containing the source code. See the Docs for additional information on how Code Engine builds [Apps, Jobs](https://cloud.ibm.com/docs/codeengine?topic=codeengine-build-config-local) and [Functions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-create-local) |
| `build-strategy` | ❌ | buildpacks | Strategy used for building the image. allowed values `dockerfile`, `buildpacks` |
| `build-size`     | ❌ | medium                      | The size of the build defines how CPU cores, memory, and disk space are assigned to the build. See the Docs for additional information for [Apps, and Jobs](https://cloud.ibm.com/docs/codeengine?topic=codeengine-plan-build#build-size) |
| `cpu`            | ❌ | 1 / 0.5                     | CPU value set for your component Default for Apps and Jobs 1 vCPU, 0.5 vCPU for Functions. [Config for Functions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-runtime), [Codeengine Memory CPU combo](https://cloud.ibm.com/docs/codeengine?topic=codeengine-mem-cpu-combo) |
| `memory`         | ❌ | 4G / 2G                     | Memory value set for your component Default for Apps and Jobs 4 GB, 2GB for Functions. [Config for Functions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-runtime), [Codeengine Memory CPU combo](https://cloud.ibm.com/docs/codeengine?topic=codeengine-mem-cpu-combo) |
| `runtime`        | ❌ | -                           | The runtime used for the Function. Currently supported `nodejs-18` and `python-3.11` see [IBM Code Engine Function Runtimes](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-runtime) for more information. |

## Usage and Example

To use this action, add it to your GitHub Actions workflow YAML file also make sure to add your IBM Cloud API Key as GitHub Action Repository Secret. There is a example for deploying an App, Job, Python/Node.js Function and a container build.

*Deploy an App: `deploy-app.yml`*: Deploy your app to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the app is`my-app`.

```yaml
name: Deploy App to Code Engine

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  deploy-app:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Deploy Application to Code Engine
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'app'
        name: 'my-app'
        build-source: './'
        cpu: 1
        memory: 4G
```

*Deploy an App: `deploy-docker-app.yml`*: Deploy your app to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository using the `dockerfile` `build-strategy` the name of the app is`my-docker-app`.

```yaml
name: Deploy App to Code Engine using Dockerfile build-strategy

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  deploy-app:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Deploy App to Code Engine using Dockerfile build-strategy
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'app'
        build-strategy: dockerfile
        name: 'my-docker-app'
        build-source: './'
        cpu: 1
        memory: 4G
```

*Deploy an App using an existing Image: `deploy-app-image.yml`*: Deploy your app to `Default` resource-group in `eu-de` to the project `MY-PROJECT` using the existing `icr.io/codeengine/helloworld:latest` image the name of the app is`my-app`.

```yaml
name: Deploy Application to Code Engine using existing container image

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  deploy-app:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Deploy Application to Code Engine using existing container image
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'app'
        name: 'my-img-app'
        image: icr.io/codeengine/helloworld:latest
        cpu: 1
        memory: 4G
```

*Deploy a Job: `deploy-job.yml`*: Deploy your Job to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the job is`my-job`.

```yaml
name: Deploy App to Code Engine

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  deploy-job:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Deploy Job to Code Engine
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'job'
        name: 'my-job'
        build-source: './'
        cpu: 1
        memory: 4G
```

*Deploy a Node.js Function: `deploy-nodejs-func.yml`*: Deploy your Node.js Function to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the function is`my-js-fn`.

```yaml
name: Deploy a Node.js Function to Code Engine

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  deploy-nodejs-junc:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Deploy JavaScript Function to Code Engine
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'fn'
        runtime: nodejs-18
        name: 'my-js-fn'
        build-source: './'
        cpu: 1
        memory: 4G
```

*Deploy a Python Function: `deploy-python-func.yml`*: Deploy your Python Function to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the function is`my-py-fn`.

```yaml
name: Deploy a Python Function to Code Engine

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  fn-py:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Deploy Python Function to Code Engine
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'fn'
        runtime: python-3.11
        name: 'my-py-fn'
        build-source: './'
        cpu: 1
        memory: 4G
```

*Building a Container Image: `build-container.yml`*: Build and push a Container Image `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the image is`private.de.icr.io/my-namespace/my-image:latest` the build name is `ce-build-image-{timestapm}` appended with the current timestamp using the auto generated regstry secret `ce-auto-icr-private-eu-de`.

```yaml
name: Build and push to ICR

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:

  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Check out code
      uses: actions/checkout@v3

    - name: Build and push to ICR
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'build'
        name: 'ce-build-image'
        image: private.de.icr.io/my-namespace/my-image:latest
        registry-secret: ce-auto-icr-private-eu-de
        build-source: './'
```