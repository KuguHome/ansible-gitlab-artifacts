Ansible role to manage Gitlab artifacts
---------------------------------------

This role downloads and deploys the latest gitlab artifact from a specific project
It is based upon https://gitlab.com/chstudio/ansible/gitlab-artifacts by Stéphane Hulard <s.hulard@chstudio.fr>

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
* `gitlabartifact_server`: The base server URL of your GitLab instance "https://gitlab.com"
* `gitlabartifact_api_token`: The API token as acquired from `/profile/personal_access_tokens` in Gitlab
* `gitlabartifact_project_id`: The complete path of the project (including all namespaces/groupnames/projectnames) with all `/` replaced by `%2F`
* `gitlabartifact_artifact_path`: The path to the artifact within the artifacts browser
* `gitlabartifact_artifact_name`: The filename of the artifact to download
* `gitlabartifact_git_reference`: e.g. the branch name, tag or commit hash of the git revision the artifact was built from
* `gitlabartifact_tempstorage`: The place on the local machine running the ansible playbook to temporarily store the artifact downloaded from Gitlab
* `gitlabartifact_name`: A freely chosen name used as a dict key for the local tempfile location in `gitlabartifact_localfile`
* `gitlabartifact_remote_destination`: The target remote location to deploy the artifact to
* `gitlabartifact_restart_services`: System services to restart after deployment
* `gitlabartifact_cleanup_local`: Whether to delete the artifact from the local machine after deploying to remote

Some more internal variables can be found in `defaults/main.yml`.

## Output variable
`gitlabartifact_localfile`: After running the role, the full path/filename of the downloaded artifact can be found in this variable, identified by the key given in `gitlabartifact_name`. This can be used to e.g. deploy the file separately vie Ansible's `file` menu
