# github-actions

This is a project where I test GitHub Actions.

A high level overview what I am setting up here:

# The CI part of the pipeline
1. I have a Python Application. The application source code is stored in "python-app-src" directory

2. I have a dockerfile to build an image for running my Python App.

3. I have the Github Actions pipeline defined here - .github/workflows/github-actions-demo.yml

4. The Github Actions pipeline is triggered :
    - when any changes are pushed to "python-app-src" directory
    - Or, any change is made to Dockerfile

5. The Github Actions pipeline does the following:
    - checksout the code
    - runs a linter against the code
    - logs into Dockerhub
    - builds and pushes an image to Dockerhub
    - scans the container image for vulnerabilities


# To-Do. CD part of the pipeline
1. when a new docker image is pushed to Dockerhub, the Pipeline should take the necessary actions to
    - update helm-chart/namespaces/dev/version.yaml with the new image tag.
      This will cause ArgoCD to automatically upgrade the App in dev environment
    - Promote the App to UAT and the Prod.


# The Helm Chart
1. A helm chart defined - under "helm-char" directory - to deploy the Python App onto K8s
2. I want to have 3 environments for the App - dev, uat and prod
3. The helm-chart/namespaces directory defines the values specific for each environment


# ArgoCD
1. I use ArgoCD to deploy the 3 environments using the same helm chart
2. I have ArgoCD Application Manifests defined for deploying the 3 environments. The "argo-cd" directory contains the manifests


# ArgoCD App-of-Apps
1. I also have a ArgoCD App-of-Apps Application manifest file (argocd-app-of-apps.yaml) which is used to deploy the Application Manifests in "argo-cd" directory.
2. With this setup, any changes made to the contents with in the helm-chart or argo-cd directory are automatically deleted and reconciled by ArgoCD.


# Deployment
1. Deploy ArgoCD Helm chart from ArtifactHub, into argo namespace
2. From the root of the repo, run - kubectl create -f argocd-app-of-apps.yaml -n argo
3. Create port forwarding to access the App in different environments:
  - k port-forward service/python-app-prod  8083:80 -n prod
  - k port-forward service/python-app-uat  8082:80 -n uat
  - k port-forward service/python-app-dev  8081:80 -n dev
