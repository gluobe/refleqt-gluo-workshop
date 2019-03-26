# Lab 04 - CI/CD pipeline

To create a CI/CD pipeline we will be using a file called `.gitlab-ci.yml`.  In 
this file all the different steps of our pipeline will be defined.  As soon as 
this file has been added to our repository the fire CI/CD pipeline will be 
started.

## Task 1: Adding '.gitlab-ci-yml' to the repository

Click on the `Project` folder to browse to your initial repository. You will see
a textbox with the branch you are working on. This will be `master` followed
with `crapapp` and a `/`. In the end you will find a `+`, click on it
and select `New file`.



The new window will allow you to enter a `title` and a `body` for the file. 
Create the following file.

**title** : `.gitlab-ci.yml`

**content**

```
image: docker:latest
services:
  - docker:dind

stages:
  - test-unit
  - build
  - deploy-test
  - test-frontend
  - deploy-prod

Test:
  stage: test-unit
  script:
     - npm install
     - npm test
  image: node:10.14

Build:
  stage: build
  script:
    - docker build -t registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-frontend:$CI_PIPELINE_ID .
    - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com
    - docker push registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-frontend:$CI_PIPELINE_ID

Deploy-test:
  stage: deploy-test
  script:
    - apk update  && apk add --no-cache curl
    - curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    - chmod +x ./kubectl && mv ./kubectl /usr/local/bin/kubectl
    - mkdir -p $HOME/.kube
    - kubectl config set-cluster $KUBE_SERVER_NAME --server=$KUBE_URL --insecure-skip-tls-verify=true
    - kubectl config set-credentials admin --username=$KUBE_USERNAME --password=$KUBE_PASSWORD
    - kubectl config set-context default --cluster=$KUBE_SERVER_NAME --user=$KUBE_USERNAME
    - kubectl config use-context default
    - kubectl set image deployment.v1.apps/frontend frontend=registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-frontend:$CI_PIPELINE_ID -n $USER_ENV-test
    # - sed 's/USERNAME_PLACEHOLDER/$GITLAB_USER_LOGIN/g' templates frontend_deployment.yaml'
    # - kubectl apply -f templates -n $USER_ENV-test

Test-fronted:
  stage: test-frontend
  before_script:
    - apt-get update && apt-get install -y curl
  script:
    - bash uitest.sh
  image: ubuntu

Deploy-prod:
  stage: deploy-prod
  script:
    - apk update  && apk add --no-cache curl
    - curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    - chmod +x ./kubectl && mv ./kubectl /usr/local/bin/kubectl
    - mkdir -p $HOME/.kube
    - kubectl config set-cluster $KUBE_SERVER_NAME --server=$KUBE_URL --insecure-skip-tls-verify=true
    - kubectl config set-credentials admin --username=$KUBE_USERNAME --password=$KUBE_PASSWORD
    - kubectl config set-context default --cluster=$KUBE_SERVER_NAME --user=$KUBE_USERNAME
    - kubectl config use-context default
    - kubectl set image deployment.v1.apps/frontend frontend=registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-frontend:$CI_PIPELINE_ID -n $USER_ENV-prod
    # - sed 's/USERNAME_PLACEHOLDER/$GITLAB_USER_LOGIN/g' templates frontend_deployment.yaml'
    # - kubectl apply -f templates -n $USER_ENV-test
```

The above should be prette self explanatory, we basically have different stages 
in which we will run different commands.

You will see also see some of the variables we entered in the `Gitlab settings` 
and you will see a few new ones.

The variables we entered in `Gitlab` are used to authorize against the `kubernetes
cluster` and the new variables are to tag the images. All pushed images in the
registry wil get a tag.

As soon as you save the file the first execution of the pipeline should start 
automatically.