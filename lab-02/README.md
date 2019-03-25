# Lab 01 - Open repository to the public

Do this for both repositories. 

In this lab we will put the repository visibilty on `public` this is needed
because we are going to pull the created images to the cluster. When the repository
would be private you would need a `kubernetes pull secret` but this is out of
scope for the workshop.

## Task 1: Open repository to the public

Click on `Settings` on the left side of the project and click `General`. In
`General settings` you can find the second tab `Visibility, project features,
permissions`. Use the `expand` tab to check out these settings.

`Project visibility` is on `Private` now but select the `Public` option from the
`dropdown box`.

Scroll down a bit and click on `Save changes`. Your repository is now public and
you will be able to pull images without a secret.
