[![Build Status](https://img.shields.io/azure-devops/build/aussiedevcrew/d4700b68-d4e4-4c1c-8802-b3031bfa80b0/17.svg?label=build-azure-databricks-api&style=flat-square&logo=data%3Aimage%2Fpng%3Bbase64%2CiVBORw0KGgoAAAANSUhEUgAAADQAAAAyCAMAAAAk%2FwjEAAAAXVBMVEX%2F%2F%2F%2BTk5Obm5udnZ2kpKSlpaWnp6eoqKiwsLCxsbGysrKzs7O6urq7u7u9vb3GxsbHx8fIyMjJycnR0dHS0tLT09PU1NTd3d3e3t7f39%2Fo6Ojp6enq6ur09PT%2F%2F%2F%2Bel%2BNbAAAAAXRSTlMAQObYZgAAAM5JREFUeNrt090OgjAMhuEqCoqKv8Mprvd%2FmUY6U9SFfSwemMh7toMno82gsd%2BpcNxtgpiMX6sR5BKQ5eGoYOlARDWKvMnawwpDVsyFpBuCSpZyPccRv6EcQA37KpKqONrxM9e52VCgJQdq9OYg4nDWWGYcaemoTECWhqM94UhX%2F21kHbI4zcm0n8pNe%2F9v63F8bZrRl6fNCWgT2UDcsEHMop1C5zpRf%2FL5TiZ3MJq1yzvLCUc6WgJaM47U4Oh6fKRrQJCPumoLI1UNjf1ddw%2FHSv3TGNoxAAAAAElFTkSuQmCC)](https://dev.azure.com/aussiedevcrew/azure-databricks-operator/_build/latest?definitionId=17&branchName=master)


[![Build Status](https://img.shields.io/azure-devops/build/aussiedevcrew/d4700b68-d4e4-4c1c-8802-b3031bfa80b0/18.svg?label=build-azure-databricks-operator&style=flat-square&logo=data%3Aimage%2Fpng%3Bbase64%2CiVBORw0KGgoAAAANSUhEUgAAADQAAAAyCAMAAAAk%2FwjEAAAAXVBMVEX%2F%2F%2F%2BTk5Obm5udnZ2kpKSlpaWnp6eoqKiwsLCxsbGysrKzs7O6urq7u7u9vb3GxsbHx8fIyMjJycnR0dHS0tLT09PU1NTd3d3e3t7f39%2Fo6Ojp6enq6ur09PT%2F%2F%2F%2Bel%2BNbAAAAAXRSTlMAQObYZgAAAM5JREFUeNrt090OgjAMhuEqCoqKv8Mprvd%2FmUY6U9SFfSwemMh7toMno82gsd%2BpcNxtgpiMX6sR5BKQ5eGoYOlARDWKvMnawwpDVsyFpBuCSpZyPccRv6EcQA37KpKqONrxM9e52VCgJQdq9OYg4nDWWGYcaemoTECWhqM94UhX%2F21kHbI4zcm0n8pNe%2F9v63F8bZrRl6fNCWgT2UDcsEHMop1C5zpRf%2FL5TiZ3MJq1yzvLCUc6WgJaM47U4Oh6fKRrQJCPumoLI1UNjf1ddw%2FHSv3TGNoxAAAAAElFTkSuQmCC)](https://dev.azure.com/aussiedevcrew/azure-databricks-operator/_build/latest?definitionId=18&branchName=master)


# Azure Databricks operator

## Introduction

Azure Databricks operator contains two projects. The golang application is a Kubernetes controller that watches CRDs that defines a Databricks job and The Python Flask App which sends commands to the Databricks.

![alt text](docs/images/azure-databricks-operator.jpg "high level architecture")

The project was built using

1. [Kubebuilder](https://book.kubebuilder.io/)
2. [Swagger Codegen](https://github.com/swagger-api/swagger-codegen)
3. [Flask-RESTPlus](http://flask-restplus.readthedocs.io)
4. [Flask](http://flask.pocoo.org/)

![alt text](docs/images/development-flow.jpg "development flow")

### Prerequisites And Assumptions

1. You have [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/),[Kind](https://github.com/kubernetes-sigs/kind) or docker for desktop installed on your local computer with RBAC enabled.
2. You have a Kubernetes cluster running.
3. You have the kubectl command line (kubectl CLI) installed.
4. You have Helm and Tiller installed.

* Configure a Kubernetes cluster in your machine
    > You need to make sure a kubeconfig file is configured.
    > if you opt AKS, you can use: `az aks get-credentials --resource-group $RG_NAME --name $Cluster_NAME`

Basic commands to check your cluster

```shell
    kubectl config get-contexts
    kubectl cluster-info
    kubectl version
    kubectl get pods -n kube-system
```

#### Kubernetes on WSL
    
on windows command line run `kubectl config view` to find the values of [windows-user-name],[minikubeip],[port]

```shell
mkdir ~/.kube \
&& cp /mnt/c/Users/[windows-user-name]/.kube/config ~/.kube

kubectl config set-cluster minikube --server=https://<minikubeip>:<port> --certificate-authority=/mnt/c/Users/<windows-user-name>/.minikube/ca.crt
kubectl config set-credentials minikube --client-certificate=/mnt/c/Users/<windows-user-name>/.minikube/client.crt --client-key=/mnt/c/Users/<windows-user-name>/.minikube/client.key
kubectl config set-context minikube --cluster=minikube --user=minikub

```

More info:

1. https://devkimchi.com/2018/06/05/running-kubernetes-on-wsl/
2. https://www.jamessturtevant.com/posts/Running-Kubernetes-Minikube-on-Windows-10-with-WSL/

### How to use operator

*Docs are work in progress*

1. Create a secret set values of `DATABRICKS_HOST` and `DATABRICKS_TOKEN`

    ```shell
    kubectl create secret testdatabricks --from-literal=DatabricksHost="https://xxxx.azuredatabricks.net" --from-literal=DatabricksToken="xxxxx"
    ```

    Make sure your secrete name is set correctly in `databricks-operator/config/default/azure_databricks_api_image_patch.yaml`

2. To install NotebookJob CRD in the configured Kubernetes cluster in ~/.kube/config, run `kubectl apply -f databricks-operator/config/crds` or `make install -C databricks-operator`

3. To deploy controller in the configured Kubernetes cluster in ~/.kube/config, run `kustomize build databricks-operator/config | kubectl apply -f -`

4. Change NotebookJob name from `sample1run1` to your desired name, set Databricks notebook path and update the values in `microsoft_v1beta2_notebookjob.yaml`

    ```shell
    kubectl apply -f databricks-operator/config/samples/microsoft_v1beta2_notebookjob.yaml
    ```

5. Basic commands to check the new Notebookjob
    
    ```shell
    kubectl get crd
    kubectl -n databricks-operator-system get svc
    kubectl -n databricks-operator-system get pod
    kubectl -n databricks-operator-system describe  pod databricks-operator-controller-manager-0
    kubectl -n databricks-operator-system logs  databricks-operator-controller-manager-0 -c dbricks -f
    kubectl get notebookjob
    kubectl describe notebookjob kubectl sample1run1
    ```

### How to extend the opertaor and build your own images

#### Updating databricks operator:

This Repo is generated by [Kubebuilder](https://book.kubebuilder.io/).

To Extend the operator `databricks-operator`:

1. Run `dep ensure` to download dependencies. It doesn't show any progress bar and takes a while to download all of dependencies.
2. Update `pkg\apis\microsoft\v1beta1\notebookjob_types.go`.
3. Regenerate CRD `make manifests`.
4. Install updated CRD `make install`
5. Generate code `make generate`
6. Update operator `pkg\controller\notebookjob\notebookjob_controller.go`
7. Update tests and run `make test`
8. Build `make build`
9. Deploy

    ```shell
    make docker-build IMG=azadehkhojandi/databricks-operator
    make docker-push IMG=azadehkhojandi/databricks-operator
    make deploy
    ```
## Main Contributors

1. Jordan Knight [Github](https://github.com/jakkaj), [Linkedin](https://www.linkedin.com/in/jakkaj/)
2. Paul Bouwer [Github](https://github.com/paulbouwer), [Linkedin](https://www.linkedin.com/in/pbouwer/)
3. Lace Lofranco [Github](https://github.com/devlace), [Linkedin](https://www.linkedin.com/in/lacelofranco/)
4. Allan Targino [Github](https://github.com/allantargino), [Linkedin](https://www.linkedin.com/in/allan-targino//)
5. Rian Finnegan [Github](https://github.com/xtellurian), [Linkedin](https://www.linkedin.com/in/rian-finnegan-97651b55/)
6. Jason Goodselli [Github](https://github.com/JasonTheDeveloper), [Linkedin](https://www.linkedin.com/in/jason-goodsell-2505a3b2/)
7. Craig Rodger [Github](https://github.com/crrodger), [Linkedin](https://www.linkedin.com/in/craigrodger/)
8. Justin Chizer [Github](https://github.com/justinchizer), [Linkedin](https://www.linkedin.com/in/jchizer/)
9. Azadeh Khojandi [Github](https://github.com/Azadehkhojandi), [Linkedin](https://www.linkedin.com/in/azadeh-khojandi-ba441b3/)

## Resources

### Build pipelines
1. [Create a pipeline and add a status badge to Github](https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)
2. [Customize status badge with shields.io](https://shields.io/)


## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
