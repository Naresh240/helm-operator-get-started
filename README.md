# helm-operator-get-started

# EKS Cluster Setup:
  [EKS Cluster Setup](https://github.com/Naresh240/eks-cluster-setup/blob/main/README.md)
# Install fluxctl:
    wget https://github.com/fluxcd/flux/releases/download/1.21.0/fluxctl_linux_amd64
    mv fluxctl_linux_amd64 fluxctl
    chmod +x fluxctl
    mv fluxctl /usr/bin/
    fluxctl version
# Install Helm3:
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
    chmod 700 get_helm.sh
    export PATH=$PATH:/usr/local/bin
    ./get_helm.sh
# Install FluxCD or Flux Operator:
  Create the flux namespace:
    
    kubectl create ns fluxcd
  
  Then, install Flux in your cluster (replace YOURUSER with your GitHub username):
  Syntax:    
    
    export GHUSER="YOURUSER"
        fluxctl install \
        --git-user=${GHUSER} \
        --git-email=${GHUSER}@users.noreply.github.com \
        --git-url=git@github.com:${GHUSER}/flux-get-started \		--> Replace URL with our Git Repo URL
        --git-path=namespaces,workloads \				            --> Replace with folder inside Git Repo
        --namespace=flux | kubectl apply -f -
  
  Changed as:
    
    export GHUSER="naresh240"
    fluxctl install \
    --git-user=${GHUSER} \
    --git-email=${GHUSER}@users.noreply.github.com \
    --git-url=git@github.com:${GHUSER}/helm-chart-nginx \
    --git-path=./ \
    --namespace=fluxcd | kubectl apply -f -

  Wait for Flux to start:
    
    kubectl -n fluxcd rollout status deployment/flux
  
  Check if Flux deployment is successful or not:
  
    kubectl get deploy -n fluxcd
    
  Authorise Flux CD to Connect to Your Git Repository:
  
  We now need to allow the Flux CD operator to interact with the Git repository, and therefore, we need to add its public SSH key to the repo.
  
  Get the public SSH key using fluxctl:
    
    fluxctl identity --k8s-fwd-ns fluxcd
    
  In order to sync your cluster state with git you need to copy the public key and create a deploy key with write access on your GitHub repository.
  Open GitHub Repo --> settings --> Deploy Keys
  Add public key inside Deploy Keys
  Click on Allow write access check box and then click on Add Key
  
  ![image](https://user-images.githubusercontent.com/58024415/102525038-c1d7ab00-40bf-11eb-81ed-62ad06469429.png)

# Install Helm Operator:
  1. Clone helm operator code:
    
    git clone https://github.com/Naresh240/helm-operator-get-started.git
    cd helm-operator-get-started
  2. "fluxcd" to be add to our repositories
    
    helm repo add fluxcd https://charts.fluxcd.io
  3. Install the HelmRelease Kubernetes custom resource definition:
    
    kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml
  4. Install Flux Helm Operator with Helm v3 support:
  
    helm upgrade -i helm-operator fluxcd/helm-operator --wait --namespace fluxcd --set git.ssh.secretName=flux-git-deploy --set helm.versions=v3
  5. Keep Helm Release file inside our GitOps Repo and check whether deployment done or not
