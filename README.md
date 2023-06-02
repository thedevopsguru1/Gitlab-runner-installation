# Gitlab-runner-installation
### Go to your Gitlab repo and . settting , then CI/CD , then runners. 
### Next the new project runner, click on three dat , then copy and paste it in the values file adn line 49.
### Then run these commands:
#### Create a Service Account named priv-sa
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-pods
  namespace: kube-system
subjects:
  - kind: ServiceAccount
    name: priv-sa
    namespace: gitlab-runner
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io

```
```
kubectl create ns gitlab-runner-project-name
```
```
helm install -n gitlab-runner gitlab gitlab/gitlab-runner -f values.yaml
```
## here is the value file
```
#GitLab Runner Image

##

## By default it's using gitlab/gitlab-runner:alpine-v{VERSION}

## where {VERSION} is taken from Chart.yaml from appVersion field

##

## ref: https://hub.docker.com/r/gitlab/gitlab-runner/tags/

##

# image: gitlab/gitlab-runner:alpine-v11.6.0

 

## Specify a imagePullPolicy

## 'Always' if imageTag is 'latest', else set to 'IfNotPresent'

## ref: http://kubernetes.io/docs/user-guide/images/#pre-pulling-images

##

imagePullPolicy: Always

 

## The GitLab Server URL (with protocol) that want to register the runner against

## ref: https://docs.gitlab.com/runner/commands/README.html#gitlab-runner-register

##

gitlabUrl: https://gitlab.com/

 

## The Registration Token for adding new Runners to the GitLab Server. This must

## be retrieved from your GitLab Instance.

## ref: https://docs.gitlab.com/ce/ci/runners/README.html

##
## Go to Steiings, CICD , then Runner, next to new project runner , click on the three dat and copy it there
runnerRegistrationToken: "GR1348"

 

## The Runner Token for adding new Runners to the GitLab Server. This must

## be retrieved from your GitLab Instance. It is token of already registered runner.

## ref: (we don't yet have docs for that, but we want to use existing token)

##

# runnerToken: ""

#

## Unregister all runners before termination

##

## Updating the runner's chart version or configuration will cause the runner container

## to be terminated and created again. This may cause your Gitlab instance to reference

## non-existant runners. Un-registering the runner before termination mitigates this issue.

## ref: https://docs.gitlab.com/runner/commands/README.html#gitlab-runner-unregister

##

unregisterRunners: true

 

## When stopping ther runner, give it time to wait for it's jobs to terminate.

##

## Updating the runner's chart version or configuration will cause the runner container

## to be terminated with a graceful stop request. terminationGracePeriodSeconds

## instructs Kubernetes to wait long enough for the runner pod to terminate gracefully.

## ref: https://docs.gitlab.com/runner/commands/#signals

terminationGracePeriodSeconds: 3600

 

## Set the certsSecretName in order to pass custom certficates for GitLab Runner to use

## Provide resource name for a Kubernetes Secret Object in the same namespace,

## this is used to populate the /home/gitlab-runner/.gitlab-runner/certs/ directory

## ref: https://docs.gitlab.com/runner/configuration/tls-self-signed.html#supported-options-for-self-signed-certificates

##

# certsSecretName:

 

## Configure the maximum number of concurrent jobs

## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-global-section

##

concurrent: 10

 

## Defines in seconds how often to check GitLab for a new builds

## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-global-section

##

checkInterval: 30

 

## Configure GitLab Runner's logging level. Available values are: debug, info, warn, error, fatal, panic

## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-global-section

##

# logLevel:

 

## Configure GitLab Runner's logging format. Available values are: runner, text, json

## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-global-section

##

# logFormat:

 

## For RBAC support:

rbac:

  create: true

  ## Define specific rbac permissions.

  # resources: ["pods", "pods/exec", "secrets"]

  # verbs: ["get", "list", "watch", "create", "patch", "delete"]

  resources: ["pods","pods/exec","secrets","pods/attach","configmaps"]

  verbs: ["get","list","watch","create","patch","delete","update"]

 

  ## Run the gitlab-bastion container with the ability to deploy/manage containers of jobs

  ## cluster-wide or only within namespace

  clusterWideAccess: false

 

  ## Use the following Kubernetes Service Account name if RBAC is disabled in this Helm chart (see rbac.create)

  ##

  #serviceAccountName: gitlab-deploy-sa

 

## Configure integrated Prometheus metrics exporter

## ref: https://docs.gitlab.com/runner/monitoring/#configuration-of-the-metrics-http-server

metrics:

  enabled: true

 

## Configuration for the Pods that that the runner launches for each new job

##

runners:

  ## Default container image to use for builds when none is specified

  ##

  image: ubuntu:16.04

 

  ## Specify one or more imagePullSecrets

  ##

  ## ref: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

  ##

  #imagePullSecrets: [camden]

 

  ## Specify the image pull policy: never, if-not-present, always. The cluster default will be used if not set.

  ##

  imagePullPolicy: "always"

 

  ## Defines number of concurrent requests for new job from GitLab

  ## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-runners-section

  ##

  # requestConcurrency: 1

 

  ## Specify whether the runner should be locked to a specific project: true, false. Defaults to true.

  ##

  # locked: true

 

  ## Specify the tags associated with the runner. Comma-separated list of tags.

  ##

  ## ref: https://docs.gitlab.com/ce/ci/runners/#using-tags

  ##

  tags: "Runner_name_tag"

 

  ## Specify if jobs without tags should be run.

  ## If not specified, Runner will default to true if no tags were specified. In other case it will

  ## default to false.

  ##

  ## ref: https://docs.gitlab.com/ce/ci/runners/#allowing-runners-with-tags-to-pick-jobs-without-tags

  ##

  runUntagged: false

 

  ## Run all containers with the privileged flag enabled

  ## This will allow the docker:dind image to run if you need to run Docker

  ## commands. Please read the docs before turning this on:

  ## ref: https://docs.gitlab.com/runner/executors/kubernetes.html#using-docker-dind

  ##

  privileged: true

  outputLimit: 160192

  ## The name of the secret containing runner-token and runner-registration-token

  # secret: gitlab-runner

 

  ## Namespace to run Kubernetes jobs in (defaults to the same namespace of this release)

  ##

  # namespace:

 

  ## Distributed runners caching

  ## ref: https://gitlab.com/gitlab-org/gitlab-runner/blob/master/docs/configuration/autoscale.md#distributed-runners-caching

  ##

  ## If you want to use s3 based distributing caching:

  ## First of all you need to uncomment General settings and S3 settings sections.

  ##

  ## Create a secret 's3access' containing 'accesskey' & 'secretkey'

  ## ref: https://aws.amazon.com/blogs/security/wheres-my-secret-access-key/

  ##

  ## $ kubectl create secret generic s3access \

  ##   --from-literal=accesskey="YourAccessKey" \

  ##   --from-literal=secretkey="YourSecretKey"

  ## ref: https://kubernetes.io/docs/concepts/configuration/secret/

  ##

  ## If you want to use gcs based distributing caching:

  ## First of all you need to uncomment General settings and GCS settings sections.

  ##

  ## Access using credentials file:

  ## Create a secret 'google-application-credentials' containing your application credentials file.

  ## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-runnerscachegcs-section

  ## You could configure

  ## $ kubectl create secret generic google-application-credentials \

  ##   --from-file=gcs-applicaton-credentials-file=./path-to-your-google-application-credentials-file.json

  ## ref: https://kubernetes.io/docs/concepts/configuration/secret/

  ##

  ## Access using access-id and private-key:

  ## Create a secret 'gcsaccess' containing 'gcs-access-id' & 'gcs-private-key'.

  ## ref: https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-runners-cache-gcs-section

  ## You could configure

  ## $ kubectl create secret generic gcsaccess \

  ##   --from-literal=gcs-access-id="YourAccessID" \

  ##   --from-literal=gcs-private-key="YourPrivateKey"

  ## ref: https://kubernetes.io/docs/concepts/configuration/secret/

  cache: {}

    ## General settings

    # cacheType: s3

    # cachePath: "gitlab_runner"

    # cacheShared: true

 

    ## S3 settings

    # s3ServerAddress: s3.amazonaws.com

    # s3BucketName:

    # s3BucketLocation:

    # s3CacheInsecure: false

    # secretName: s3access

 

    ## GCS settings

    # gcsBucketName:

    ## Use this line for access using access-id and private-key

    # secretName: gcsaccess

    ## Use this line for access using google-application-credentials file

    # secretName: google-application-credentials

 

  ## Build Container specific configuration

  ##

  builds:

    cpuLimit: "4"

    memoryLimit: "8Gi"

    cpuRequests: "250m"

    memoryRequests: "1Gi"

 

  ## Service Container specific configuration

  ##

  services:

    cpuLimit: "2"

    memoryLimit: "8Gi"

    cpuRequests: "250m"

    memoryRequests: "1Gi"

 

  ## Helper Container specific configuration

  ##

  helpers:

    cpuLimit: "2"

    memoryLimit: "4Gi"

    cpuRequests: "250m"

    memoryRequests: "1Gi"

    # image: gitlab/gitlab-runner-helper:x86_64-latest

 

  ## Service Account to be used for runners

  ##

  serviceAccountName: priv-sa # add the sa that is in shellback cluster or whatever cluster you are working on

 

  ## If Gitlab is not reachable through $CI_SERVER_URL

  ##

  # cloneUrl:

 

  ## Specify node labels for CI job pods assignment

  ## ref: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/

  ##

  nodeSelector: {}

    #kubernetes.io/hostname: nsetk8sorn-worker3 

 

  ## Specify pod labels for CI job pods

  ##

  podLabels:

    runner: running

 

  ## Specify annotations for job pods, useful for annotations such as iam.amazonaws.com/role

  # podAnnotations: {}

 

  ## Configure environment variables that will be injected to the pods that are created while

  ## the build is running. These variables are passed as parameters, i.e. `--env "NAME=VALUE"`,

  ## to `gitlab-runner register` command.

  ##

  ## Note that `envVars` (see below) are only present in the runner pod, not the pods that are

  ## created for each build.

  ##

  ## ref: https://docs.gitlab.com/runner/commands/#gitlab-runner-register

  ##

  # env:

  #   NAME: VALUE

 

## Configure resource requests and limits

## ref: http://kubernetes.io/docs/user-guide/compute-resources/

##

resources:

  limits:

    memory: "4Gi"

    cpu: "500m"

  requests:

    memory: "1Gi"

    cpu: "250m"

 

## Affinity for pod assignment

## Ref: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity

##



...

```

