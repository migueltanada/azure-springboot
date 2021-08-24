## Azure spring starter
<br>
<br>
<br>

# Create an AKS + APIM cluster (will work with free account)

Services that will be used
1. Azure AKS (external, publicly accessible)
    1. Kubernetes ingress controller
    1. Microserice Deployment (pod + service)
1. Azure APIM (external, publicly accessible)

![figure1](ss/figure1.JPG)


## Create Resource group

1. open azure console @ [portal.azure.com](https://portal.azure.com)

1. click on the hambburger menu button on top left corner and select **resource groups**

    ![azure_screenshot](ss/rg1.JPG)

1. click on **create**

    ![azure_screenshot](ss/rg2.JPG)

1. type in desired resource group name. for this example name will be **demo**

    ![azure_screenshot](ss/rg3.JPG)

1. click on create and you should be able to see your newly created resource group

    ![azure_screenshot](ss/rg4.JPG)

---

<br>
<br>

## Create AKS Cluster

1. open azure console @ [portal.azure.com](https://portal.azure.com)

1. click on the hambburger menu button on top left corner and select **Create a resource**

    ![azure_screenshot](ss/ak1.JPG)

1. Click on **kubernetes service > Create**

    ![azure_screenshot](ss/ak2.JPG)

1. Fill up the fields, then click __Next: Node pools__. _fields used in this demo_:
    - Resource group: demo
    - Kubernetes cluster name: demo-aks
    - region: (US) East US *_selected region should have aks available_
    - availability zone: choose only  1 zone
    - scale method: manual
    - node count 1

    ![azure_screenshot](ss/ak3.JPG)

1. Click __Next: Authentication__

    ![azure_screenshot](ss/ak4.JPG)

1. Disable RBAC Click __Next: Networking__

    ![azure_screenshot](ss/ak5.JPG)

1. Fill up fields, then click __Next: Integrations__

    ![azure_screenshot](ss/ak6.JPG)

1. Disable container monitoring and then click __Review + create__

    ![azure_screenshot](ss/ak7.JPG)

1. This will validate your configuratoins, and once it has passed click on __Create__

    ![azure_screenshot](ss/ak8.JPG)

1. Wait for deployment to finish, then click on connect to cluster

    ![azure screenshot](ss/ak9.JPG)

    ![azure screenshot](ss/ak10.JPG)

1. Run the ff commands on your local(with az and kubectl installed) CLI to connet to the cluster

    
    ```bash
    az login # This will give you a link to login your azure credentials via browser
    # it will wait for the process to complete then you will be automatically signed in to azure CLI
    # this login expires after a few hours, if you will be using az commands the following day, you will have to login again

    #these commands are available by clicking on Connect in your azure portal -- kubernetes service UI
    az account set --subscription <subscription-id>
    az aks get-credentials --resource-group demo --name demo-aks
    ```
    ![azure screenshot](ss/ak11.JPG)

---

<br>
<br>

## Clone Repo and its Submodules then deploy to aks

1. Clone the repository and its sub modules

    ```bash
    git clone git@github.com:migueltanada/azure-springboot.git --recurse-submodules
    ```

1. Create dev namespace and switch to that namespace

    ```bash
    kubectl create namespace dev
    kubectl config set-context --current --namespace dev
    ```

1. Deploy ingress contrller(optional) *_helm should be installed_

    ```bash
    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm repo update
    helm install ingress-nginx ingress-nginx/ingress-nginx --namespace dev #--create-namespace
    ```

1. Deploy mockapi *_helm should be installed_

    ```bash
    cd mockapi
    helm install latest helm_repo/mockapi --set configmaps.env=dev --namespace dev
    cd -
    ```

1. Access the API by getting the service address of your ingres controller *_this wil only work if you did step 3_

    ```bash
    kubectl get svc ingress-nginx-controller
    ## Ip to be used is the one in External-IP column
    curl -k https://<ip>/mock/config/sample1
    ```

---

<br>
<br>

## Create APIM

1. open azure console @ [portal.azure.com](https://portal.azure.com)

1. click on the hambburger menu button on top left corner and select **Create a resource**

    ![azure_screenshot](ss/ak1.JPG)

1. Search for api management and press enter

    ![azure_screenshot](ss/am1.JPG)

1. Click Create

    ![azure_screenshot](ss/am2.JPG)

1. Fill up fields and then click on __Review + create__

    ![azure_screenshot](ss/am3.JPG)

1. Once validation is complete click on __create__

1. The deployment of APIM takes more than 20 mins. The administrator email you configured will recieve an email once deployment is done.

    ![azure_screenshot](ss/am4.JPG)

1. Once you get the mail saying your APIM is ready/active, you can opn your azure portal and go to API Management

1. Click on springmockapi > API > Add > OpenApi Specification > Fill up the fields
    - OpenAPI specification: upload swagger.json from the repository you have cloned``./mockap/swagger.json``
    - display name: mockapi

    ![azure_screenshot](ss/am5.JPG)

    ![azure_screenshot](ss/am6.JPG)

1. Test the APi using test tab in azure portal

    ![azure_screenshot](ss/am7.JPG)








