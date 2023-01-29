# ansible-config-mgt


Save the facts


Jenkinsfile for a quick test
============================
```
pipeline {
    agent any

  stages {
    stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
   
   stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }

    stage('Package'){
      steps {
        script {
          sh 'echo "Packaging App" '
        }
      }
    }

    stage('Deploy'){
      steps {
        script {
          sh 'echo "Deploying to Dev"'
        }
      }

    }
    
    stage("clean Up"){
       steps {
        cleanWs()
     }
    }
     
    }
}
````

Inventory/dev.yml for project 13
================================

[nfs]
172.31.89.70 ansible_ssh_user='ec2-user'

[webservers]
172.31.93.167 ansible_ssh_user='ec2-user'
172.31.82.47 ansible_ssh_user='ec2-user'

[db]
172.31.91.145 ansible_ssh_user='ec2-user' 

[lb]
172.31.89.139 ansible_ssh_user='ubuntu



Playbooks/site.yml for Project 13
=================================

---
- name: import common file
  import_playbook: ../static-assignments/common.yml
  tags:
  - always

- name: include env-vars file
  import_playbook: ../dynamic-assignments/env-vars.yml
  tags:
  - always

- name: import database file
  import_playbook: ../static-assignments/db.yml

- name: import webservers file
  import_playbook: ../static-assignments/uat-webservers.yml

- name: import loadbalancers assignment
  import_playbook: ../static-assignments/lb.yml
  when: loadbalancer_is_required
