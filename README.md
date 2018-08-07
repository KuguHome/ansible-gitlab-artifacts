Ansible Role to manage gitlab artifacts
---------------------------------------

This role downloads and deploys the latest gitlab artifact from a specific project

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
