Ansible role to manage Gitlab artifacts
---------------------------------------

This role downloads and deploys a Gitlab artifact from a specific project.
It is based upon https://gitlab.com/chstudio/ansible/gitlab-artifacts by Stéphane Hulard <s.hulard@chstudio.fr>

The download happens completely on the local system running the Ansible playbook, so Gitlab credentials/token are not disclosed to the remote system.

The by default, the latest artifact is selected, but this can be filtered via build pipeline ID, Git reference or job name and either all artifacts or a specific file can be downloaded.

There is a deployment task (including a customizable system service restart handler) to a remote system included for simple cases, but the deployment can also be handled externally e.g. by the surrounding playbook.


## Example

```ansible-playbook
- name: Install prometheus-metrics
  include_role:
    name: gitlab-artifacts
  vars:
    gitlabartifact_project_id: "openhab%2Fprometheus-metrics"
    gitlabartifact_artifact_path: "com.kuguhome.openhab.prometheusmetrics/target/"
    gitlabartifact_artifact_name: "com.kuguhome.openhab.prometheusmetrics-2.4.0-SNAPSHOT.jar"
    gitlabartifact_remote_destination: "/usr/share/openhab2/addons/"
    gitlabartifact_restart_services:
    - "openhab2"
```

## Available Varaiables
* `gitlabartifact_server`: The server name of your Gitlab instance, e.g. "gitlab.com"
* `gitlabartifact_api_token`: The API token as acquired from `/profile/personal_access_tokens` in Gitlab
* `gitlabartifact_project_id`: The complete path of the project (including all namespaces/groupnames/projectnames) with all `/` replaced by `%2F`
* `gitlabartifact_artifact_path`: The path to the artifact within the artifacts browser.
* `gitlabartifact_artifact_name`: The filename of the artifact to download. If not defined, the whole artifacts.zip will be downloaded.
* `gitlabartifact_git_reference`: e.g. the branch name, tag or commit hash of the git revision the artifact was built from
* `gitlabartifact_jobname`: The CI job name which created the atrifact
* `gitlabartifact_pipeline_id`: The ID of the pipeline which produced the artifact
* `gitlabartifact_tempstorage`: The place on the local machine running the ansible playbook to temporarily store the artifact downloaded from Gitlab
* `gitlabartifact_name`: A freely chosen name used as a dictionary key for the local tempfile location in `gitlabartifact_localfile`
* `gitlabartifact_remote_destination`: The target remote location to deploy the artifact to
* `gitlabartifact_restart_services`: System services to restart after deployment
* `gitlabartifact_cleanup_local`: Whether to delete the artifact from the local machine after deploying to remote
* `gitlabartifact_extract`: If the whole artifacts.zip is downloaded, this flag determines whether the archive is extracted to the local machine.

Some more internal variables can be found in `defaults/main.yml` and `vars/main.yml`.

## Output variable
`gitlabartifact_localfile`: After running the role, the full path/filename of the downloaded artifact can be found in this variable, identified by the key given in `gitlabartifact_name`. This can be used to e.g. deploy the file separately vie Ansible's `unarchive` module etc.

## Alternate deployment
Instead of downloading build artifacts to the local machine and pushing them again to a remote host, there is also a "hidden" alternate option: The Gitlab repo (not the artifacts!) can be cloned directly on the remote host from Gitlab. This needs a deployment token which can be acquired from `[repo]settings/repository#js-deploy-tokens` for each repo individually, giving only read access to this specific repo. Instead of using the aforementioned variables, use these:
* `gitlabartifact_server`: The server name of your Gitlab instance, e.g. "gitlab.com"
* `gitlabartifact_project_id`: The complete path of the project (including all namespaces/groupnames/projectnames) *without* `/` replacements
* `gitlabartifact_deploytoken_number`: The deployment token number given by Gitlab. All tokens automatically have the form `gitlab+deploy-token-NUMBER`, only the last number part goes into this variable.
* `gitlabartifact_deploytoken_storage`: The path/folder where the deploytokens are stored, each individually in a plaintext file named `gitlab+deploy-token-NUMBER` where NUMBER is the `gitlabartifact_deploytoken_number` from above
* `gitlabartifact_git_reference`: e.g. the branch name, tag or commit hash of the Git revision to check out
* `gitlabartifact_remote_destination`: The target remote location to check ot the repo into (repo contents directly go into this path without an additional containing folder in between)
* `gitlabartifact_restart_services`: System services to restart after deployment
* `gitlabartifact_clonedepth`: Default 1, override with -1 if you don't want to do a shallow clone
* `gitlabartifact_overwrite`: Overwrites local changes when checking out
