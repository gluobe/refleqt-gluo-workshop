# Lab 02 - Add environment variables to Gitlab

We will add some environment variables to the Gitlab project. These are needed
in the pipeline.

## Task 1: Browse to the environment variables

In you forked project press on the `Settings` button, you can find this on the
left side of your project. If you pressed `Settings` a couple of options will
become visable. We need to add these variables in the `CI/CD` settings. If you
pressed the `CI/CD` settings you will vind the `Environment variables`. `Expand`
this and fill in the following variables.

```
KUBE_PASSWORD = <password>
KUBE_SERVER_NAME = <server_name>
KUBE_URL = <url>
KUBE_USERNAME = <username>
USER_ENV = <user_environment>
```

The environment variables will be provided in the classroom. The `USER_ENV`
variable is for example `env01` for the first student, etc ...
