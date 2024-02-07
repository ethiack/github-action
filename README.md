<!-- MARKDOWN LINKS & IMAGES -->

[version-shield]: https://img.shields.io/github/v/release/ethiack/github-action?style=for-the-badge
[version-url]: https://github.com/ethiack/github-action/releases/latest

[license-shield]: https://img.shields.io/github/license/ethiack/github-action?style=for-the-badge
[license-url]: LICENSE

[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/company/ethiack


<!-- README -->
<a name="readme-top"></a>
<div align="center">

<h1>
  <br>
    <img src="https://raw.githubusercontent.com/ethiack/github-action/main/assets/logo.webp" alt="logo" width="400">
    <br><br>
    Ethiack GitHub Action
    <br><br>
</h1>

<h4>GitHub Action for integrating Ethiack's Public API in GitHub Workflows.</h4>

[![GitHub Release][version-shield]][version-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<hr />


[Introduction](#introduction) •
[Credentials Setup](#credentials-setup) •
[Usage](#usage) •
[License](#license)


</div>

## Introduction

This GitHub Action facilitates the integration of [Ethiack's Public API](https://api.ethiack.com) ([API docs](https://portal.ethiack.com/docs/api/)) for launching scans through GitHub workflows. By utilizing this action, you can seamlessly incorporate Ethiack's security scanning capabilities into your CI/CD pipeline, enhancing your software development lifecycle with automated security testing.

<p align="right"><small>(<a href="#readme-top">back to top</a>)</small></p>

## Credentials Setup


Using Ethiack's API - and, therefore, this Action - requires authentication using an *API Key* and *API Secret*, which can be retrieved in [Ethiack's Portal settings page](https://portal.ethiack.com/settings/api). These credentials must be available as environment variables `ETHIACK_API_KEY` and `ETHIACK_API_SECRET`, repectively, whenever this action is used.

To configure, navigate to your repository settings, select `Secrets and variables`, then `Actions`, and add the following secrets:

- `ETHIACK_API_KEY`: Your API Key
- `ETHIACK_API_SECRET`: Your API Secret

> [!CAUTION]
> Ensure that these secrets are referenced in your workflow files securely.


<p align="right"><small>(<a href="#readme-top">back to top</a>)</small></p>

## Usage


> [!NOTE]
> This GitHub action is fundamentally a wrapper around [Ethiack's Public API](https://api.ethiack.com/), using [Ethiack's Job Manager Package](https://github.com/ethiack/job-manager). For more information, see the [API docs](https://portal.ethiack.com/docs/api/) and refer to the later package.


### **Example:** *Launching a job and waiting for its conclusion*

This pipeline launches a scan for the domain  `https://example.ethiack.com` and waits until it finishes (cf. `--wait` flag). If vulnerabilities with severity `medium` or higher are found, the success of the job is interpreted as failing, and this pipeline step will exit with a non-zero status code (cf. `--fail` flag).

```yaml
jobs:
  ethiack-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Launch Ethiack Scan
        uses: ethiack/github-action@main
        with:
          command: launch
          url: https://example.ethiack.com
          args: --wait --fail --severity medium
        env:
          ETHIACK_API_SECRET: ${{ secrets.ETHIACK_API_SECRET }}
          ETHIACK_API_KEY: ${{ secrets.ETHIACK_API_KEY }}
```


### **Example:** *Checking the success of a job.*

This pipeline checks the success of a job. It will fail if the respective job has finished and vulnerabilities with severity equal or above `high` were found.


```yaml
jobs:
  ethiack-check-job-success:
    runs-on: ubuntu-latest
    steps:
      - name: Check Ethiack Job Success
        uses: ethiack/github-action@main
        with:
          command: success
          uuid: 'your-job-uuid'
          args: --severity high --fail
        env:
          ETHIACK_API_SECRET: ${{ secrets.ETHIACK_API_SECRET }}
          ETHIACK_API_KEY: ${{ secrets.ETHIACK_API_KEY }}
```

> [!NOTE]
> For retrieving the success of a job without exiting the pipeline, simply provide the flag `--no-fail` instead of `--fail` in the previous example.


### Available commands

This GitHub Action supports every command provided by [Ethiack's Job Manager Package](https://github.com/ethiack/job-manager). This includes, but is not necessarily limited to, the commands:

<div align="center">

|  Command  	|                      Description                     	| Required Inputs 	|
|:---------:	|:----------------------------------------------------:	|:---------------:	|
| `check`   	| Check if a URL is valid and a job can be submitted   	| `url`           	|
| `launch`  	| Launch a job and, optionally, wait for it to finish. 	| `url`           	|
| `info`    	| Retrieve information about a job.                    	| `uuid`          	|
| `list`    	| List all jobs for the organization.                  	| -               	|
| `status`  	| Retrieve the status of a job.                        	| `uuid`          	|
| `success` 	| Retrieve the success of a job.                       	| `uuid`          	|
| `await`   	| Wait for a job to finish.                            	| `uuid`          	|
| `cancel`  	| Cancel a queued or running job.                      	| `uuid`          	|

</div>


#### Required Inputs

> The `url` input refers to the target Uniform Resource Locator (URL) of the service for which the command is run.

> The `uuid` input refers to the Universal Unique Identifier (UUID) of the job for which the command is run.  

#### Optional Arguments
> The behaviour of these commands can be customized with flags and additional parameters provided inn the `args:` variable in the workflow step (e.g., the `--fail` and `--severity` flags in the examples above). For more information regarding the available options and flags for each command, please refer to the [Job Manager Package](https://github.com/ethiack/job-manager).

<p align="right"><small>(<a href="#readme-top">back to top</a>)</small></p>


## License
Distributed under the MIT License. See [LICENSE](LICENSE) for more information.

<p align="right"><small>(<a href="#readme-top">back to top</a>)</small></p>
