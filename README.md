# Code Engine GitHub Action

This GitHub Action allows you to interact with IBM Cloud Code Engine. Deploy Apps, Jobs, and Functions. It offers flexibility for different deployment types and provides various configuration options.

## Inputs

| Name             | Required | Default Value               | Description                                                                                                                                                                                                                                                                                         |
|------------------|---------|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `api-key`        | ✅       | -                           | IAM API Key used to log into the IBM Cloud. Please store your IBM Cloud API key securely in your GitHub repository Secrets.                                                                                                                                                                         |
| `resource-group` | ❌       | Your Default Resource Group | An IBM Cloud Resource Group, a logical container for organizing and managing related cloud resources.                                                                                                                                                                                               |
| `region`         | ✅       | -                           | The geographical area where your Code Engine project is located, like `eu-de` [codeengine-regions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-regions)                                                                                                                                  |
| `project`        | ✅       | -                           | The unique identifier (GUID) or the name that identifies your IBM Cloud Code Engine project.                                                                                                                                                                                                        |
| `component`      | ✅       | -                           | The type of component to deploy. allowed values `application`, `app`, `function`, `func`, `fn`, `job`, `subscription`, `sub`                                                                                                                                                                        |
| `subscription-type` | ❌        | -                           | The type of subscription. allowed values `cos`, `cron`, `kafka`. WARNING Currently only `cron` is supported                                                                                                                                                                                         |
| `name`           | ✅       | -                           | The name of the App, Function, or Job.                                                                                                                                                                                                                                                              |
| `build-source`   | ❌       | .                           | Path to the directory containing the source code. See the Docs for additional information on how Code Engine builds [Apps, Jobs](https://cloud.ibm.com/docs/codeengine?topic=codeengine-build-config-local) and [Functions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-create-local) |
| `build-size`     | ❌       | medium                      | The size of the build defines how CPU cores, memory, and disk space are assigned to the build. See the Docs for additional information for [Apps, and Jobs](https://cloud.ibm.com/docs/codeengine?topic=codeengine-plan-build#build-size)                                                           |
| `cpu`            | ❌       | 1 / 0.5                     | CPU value set for your component Default for Apps and Jobs 1 vCPU, 0.5 vCPU for Functions. [Config for Functions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-runtime), [Codeengine Memory CPU combo](https://cloud.ibm.com/docs/codeengine?topic=codeengine-mem-cpu-combo)          |
| `memory`         | ❌       | 4G / 2G                     | Memory value set for your component Default for Apps and Jobs 4 GB, 2GB for Functions. [Config for Functions](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-runtime), [Codeengine Memory CPU combo](https://cloud.ibm.com/docs/codeengine?topic=codeengine-mem-cpu-combo)              |
| `runtime`        | ❌       | -                           | The runtime used for the Function. Currently supported `nodejs-18` and `python-3.11` see [IBM Code Engine Function Runtimes](https://cloud.ibm.com/docs/codeengine?topic=codeengine-fun-runtime) for more information.                                                                              |
| `destination-type`       | ❌       | app                         | The type of the destination. Valid values are `app`, `function` and `job`. This value is optional.                                                                                                                                                                    |
| `destination`        | ❌       | -                           | The name of the application or job resource that you want to receive events; for example, `myapp`. If needed, use the --path option to further qualify an app destination. This value is required.                                                                                                  |
| `path`        | ❌       | -                           | The path within the destination application where events are forwarded; for example, `/events`. |
| `extension`        | ❌       | -                           | Extension used for the Subscription. Set CloudEvents extensions to send to the destination. Must be in `NAME=VALUE` format. This action adds a new CloudEvents extension or overrides an existing CloudEvent attribute. Specify one extension per --extension option; for example, `--ext extA=A --ext extB=B`. |
| `schedule`        | ❌       | -                           | Schedule how often the event is triggered, in crontab format. For example, specify `'*/2 * * * *'` (in string format) for every two minutes. By default, the cron event is triggered every minute and is set to the `UTC` time zone.    |

## Usage and Example

To use this action, add it to your GitHub Actions workflow YAML file also make sure to add your IBM Cloud API Key as GitHub Action Repository Secret. There is a example for deploying an App, Job and Python/Node.js Function.

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

*Deploy a Job: `deploy-job.yml`*: Deploy your Job to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the job is`my-job`.

```yaml
name: Deploy Job to Code Engine

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
*Deploy a Job: `deploy-job.yml`*: Deploy your Job to `Default` resource-group in `eu-de` to the project `MY-PROJECT` with its source code in the root of the repository the name of the job is`my-job`.

```yaml
name: Deploy Cron Subscription to Code Engine

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

    - name: Deploy Cron Subscription to Code Engine
      uses: IBM/code-engine-github-action@v1
      with:
        api-key: ${{ secrets.IBM_IAM_API_KEY }}
        resource-group: 'Default'
        region: 'eu-de'
        project: 'MY-PROJECT'
        component: 'sub'
        subscription-type: 'cron'
        destination-type: 'app'
        destination: 'my-app'
        path: '/events'
        extension: 'extA=A'
        schedule: '*/2 * * * *'
        name: 'my-cron-sub'
        build-source: './'
        cpu: 1
        memory: 4G
```