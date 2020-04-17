# Raddit

This is an example of a microservices application used in CI/CD demo with **Gitlab CI**, **Kubernetes**, and **Helm**

[Link to the original blog post](http://artemstar.com/2018/01/15/cicd-with-kubernetes-and-gitlab/)  

How to reproduce:

1. Create Kubernetes Cluster

2. Install Gitlab  -  https://docs.gitlab.com/charts/quickstart/index.html

3. Create service account for gitlab runner:

$ gcloud iam service-accounts create gitlab \
--description "gitlab service account" \
--display-name "gitlab-sa"

4. Bind Role to service account:

$ gcloud iam service-accounts list
NAME                                    EMAIL                                                      DISABLED
k8s                                     k8s-service-account@docker-266108.iam.gserviceaccount.com  False
gitlab                                  gitlab@docker-266108.iam.gserviceaccount.com               False
Compute Engine default service account  777693616855-compute@developer.gserviceaccount.com         False

$ gcloud iam service-accounts get-iam-policy gitlab@docker-266108.iam.gserviceaccount.com
etag: ACAB

$ gcloud projects add-iam-policy-binding docker-266108 \
--member serviceAccount:gitlab@docker-266108.iam.gserviceaccount.com \
--role roles/container.developer

5. Create key pair for this service account:

$ gcloud iam service-accounts keys create ~/.kube/gitlab_key.json \
--iam-account gitlab@docker-266108.iam.gserviceaccount.com

6. Add variable "service_account" (File type) in gitlab project and copy content of gitlab_key.json

7. Use this var in gitlab-ci.yml to make access for runner to gitlab_key:

  - gcloud auth activate-service-account --key-file ${service_account}
  - gcloud container clusters get-credentials ${CLUSTER_NAME} --zone ${ZONE} --project ${PROJECT}


# kubernetes-gitlab-example
