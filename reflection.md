GitOps repository for the retail-store-sample-app final project
Made by Guy Arnan - submission 8/12/2025
---------------------

* This repo was set up to hold argoCD manifests for each of the project services (ui, cart, checkout, orders, catalog)
* Each service has its own Chart.yaml and two values.yaml files: The default configuration is defined in values.yaml, and an additional values-dev.yaml provides a dev-specific configuration

Designed deployment flow:
1. A change is made to the /src folder of the source GH repo and is pushed to main. This triggers the GH action CI phase
2. The GH action workflow builds new Docker images for any chnages services (and only them) before pushing each to its respective ECR repository. The images are tagged with the commit SHA
3. The second phase of the same workflow writes the new images tags into the values.yaml files for the changed services in the GitOps repository (the one this document resides within)
4. Whilst monitoring the GitOps repository, ArgoCD detects the changed imaged tags and marks the changed services as OutOfSync
5. ArgoCD syncs the cluster to the desired state, rolling the Deployments to use the new images from ECR, thereby completing the deployment of the updated services


+++

This is my first time setting up a project from end to end and, although there were (and still are) several glitches I haven't been able to fully resolve, I'm quite satisfied with the results.
If I may add a few words about the experience of setting up this GitOps design pattern, I find that it's the closest to 'magic' when everything works as intended. 
Luckily the lectures by Dor taught us exactly how to do this.

ArgoCD is an incredible tool for automating the CD phase of application deployment, and I particularly like the fact that it continuously monitors its repository, thereby making it such that any comit to the repo will automatically trigger a new build (and any other measures to ensure the cluster is synced to the repo).

ArgoCD does have a few disadvantages vs. manual helm deployments. 
For instance, ArgoCD would need to initially be set up on the cluster (in addition to being upgraded, secured, monitored, etc..), which adds overhead to the project (vs simply running helm upgrade in CI)
Argo also requires continuous access to both the production cluster and Git, meaning deployments can break if a security token was rotated and wasn't updated in the Secret

+++

Summary report:
All microservices are deployed and communicating successfully within the cluster, and the GitOps/CI‑CD flow from source repo → ECR → Argo CD is fully implemented and working. 
However, the UI currently returns HTTP 500 on /home due to an internal 'IllegalArgumentException: URI is not absolute' in the sample UI image, *even though all backend services are reachable from the UI pod*
I believe that debugging this applicative bug is out of scope for this course, as the project demonstrates a complete GitOps deployment pipeline and healthy microservice communication, 
with the UI error documented as a known limitation of the current image version.


