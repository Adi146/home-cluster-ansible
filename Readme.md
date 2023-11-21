# Overview
This is my Ansible playbook to install and setup my local home cluster. At the moment it consists of a single node cluster with argocd as GitOps tool and a NFS share for persistent storage.

## Configuration
This is an example `hosts.yaml` for configuration

```Yaml
all:
  vars:
    users:
      - username: user1
        shell: /bin/zsh
        github_user: github_user1
        sudo: true
      - username: user2
        shell: /bin/bash
        github_user: github_user2
        sudo: false

masters:
  hosts:
    control-plane
  vars:
    cni: cilium
    argo_cd_bootstrap_application:
      apiVersion: argoproj.io/v1alpha1
      kind: Application
      metadata:
        name: bootstrap
        namespace: argo-cd
      spec:
        destination:
          namespace: argo-cd
          server: https://kubernetes.default.svc
        project: default
        source:
          path: charts/bootstrap
          repoURL: https://github.com/github_user1/boostrap_chart.git
          targetRevision: HEAD
          helm:
            parameters:
              - name: param_name
                value: param_value
        syncPolicy:
          automated: {}
```

## Run
Use this command to run the playbook after configuration:
```Bash
ansible-playbook -i hosts.yaml play_k8s.yaml -u my-user
```

