# Lab 03 - Create Gitlab pipeline

Do this for both repositories.

To automate the deployment of the application we need a pipeline in the Gitlab
repository.

## Task 1: Create a new file in the repository

Click on the `Project` folder to browse to your initial repository. You will see
a textbox with the branch you are working on. This will be `master` followed
with `crapapp-backend` and a `/`. In the end you will find a `+`, click on it
and select `New file`.

The new window will allow you to enter a `title` and a `body` for the file. Create
the following file.

**title** : `.gitlab-ci.yml`

**content**
```
image: docker:latest
services:
  - docker:dind

stages:
  - build
  - deploy

Build:
  stage: build
  script:
    - docker build -t registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-backend:$CI_PIPELINE_ID .
    - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com
    - docker push registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-backend:$CI_PIPELINE_ID

Deploy:
  stage: deploy
  script:
    - apk update  && apk add --no-cache curl
    - curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    - chmod +x ./kubectl && mv ./kubectl /usr/local/bin/kubectl
    - mkdir -p $HOME/.kube
    - kubectl config set-cluster $KUBE_SERVER_NAME --server=$KUBE_URL --insecure-skip-tls-verify=true
    - kubectl config set-credentials admin --username=$KUBE_USERNAME --password=$KUBE_PASSWORD
    - kubectl config set-context default --cluster=$KUBE_SERVER_NAME --user=$KUBE_USERNAME
    - kubectl config use-context default
    - kubectl set image deployment.v1.apps/backend backend=registry.gitlab.com/$GITLAB_USER_LOGIN/crapapp-backend:$CI_PIPELINE_ID -n $USER_ENV-test
```

You will see some of the variables we entered in the `Gitlab settings` and you
will see a few new ones.

The variables we entered in `Gitlab` are used to authorize against the `kubernetes
cluster` and the new variables are to tag the images. All pushed images in the
registry wil get a tag.

Now create the same file but instead of `backend` you need to use `frontend` in the
pipeline code. 
