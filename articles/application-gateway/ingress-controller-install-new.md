---
title: Bejövő adatvezérlő létrehozása új Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan helyezhet üzembe egy Application Gateway beléptetési vezérlőt egy új Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be5ac75e2da3eaeae300fd36e152a24c9777e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593739"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Application Gateway beáramlási vezérlő (AGIC) telepítése új Application Gateway használatával

Az alábbi utasítások azt feltételezik, hogy Application Gateway beáramló vezérlőt (AGIC) olyan környezetben telepítik, amely nem rendelkezik már meglévő összetevőkkel.

## <a name="required-command-line-tools"></a>Szükséges parancssori eszközök

Az alábbi parancssori műveletekhez [Azure Cloud Shell](https://shell.azure.com/) használatát javasoljuk. Indítsa el a rendszerhéjt a shell.azure.com, vagy kattintson a hivatkozásra:

[![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)

Másik lehetőségként indítsa el Cloud Shell a Azure Portal a következő ikon használatával:

![Portál elindítása](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

A [Azure Cloud Shell](https://shell.azure.com/) már rendelkezik az összes szükséges eszközzel. Ha másik környezetet szeretne használni, győződjön meg arról, hogy telepítve vannak a következő parancssori eszközök:

* `az` – Azure CLI: [telepítési utasítások](/cli/azure/install-azure-cli)
* `kubectl` -Kubernetes parancssori eszköz: [telepítési utasítások](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` -Kubernetes csomagkezelő: [telepítési utasítások](https://github.com/helm/helm/releases/latest)
* `jq` -parancssori JSON-processzor: [telepítési utasítások](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Identitás létrehozása

Az alábbi lépéseket követve hozzon létre egy Azure Active Directory (HRE) [egyszerű szolgáltatásnév-objektumot](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object). Jegyezze fel a `appId` , `password` , és `objectId` értékeket – ezeket az alábbi lépésekben fogjuk használni.

1. AD egyszerű szolgáltatás létrehozása ([További információ az Azure RBAC](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    A `appId` JSON-kimenet és az `password` értékek a következő lépésekben lesznek felhasználva


1. Az `appId` új egyszerű szolgáltatás beszerzéséhez használja az előző parancs kimenetét `objectId` :
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    A parancs kimenete a `objectId` következő Azure Resource Manager sablonban lesz felhasználva

1. Hozza létre a paramétert, amelyet később a Azure Resource Manager sablon üzembe helyezéséhez fog használni.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Egy **KUBERNETES RBAC** -kompatibilis fürt üzembe helyezéséhez állítsa be a `aksEnableRBAC` mezőt `true`

## <a name="deploy-components"></a>Összetevők üzembe helyezése
Ez a lépés a következő összetevőket adja hozzá az előfizetéséhez:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Virtual Network](../virtual-network/virtual-networks-overview.md) 2 [alhálózattal](../virtual-network/virtual-networks-overview.md)
- [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
- [Felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md), amelyet a [HRE Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md) használ majd

1. Töltse le a Azure Resource Manager sablont, és szükség szerint módosítsa a sablont.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Telepítse a Azure Resource Manager sablont a használatával `az cli` . Ez akár 5 percet is igénybe vehet.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Miután az üzembe helyezés befejeződött, töltse le a központi telepítési kimenetet egy nevű fájlba `deployment-outputs.json` .
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Application Gateway bejövő adatkezelő beállítása

Az előző szakaszban ismertetett utasítások alapján létrehozunk és konfiguráltunk egy új AK-fürtöt és egy Application Gateway. Most már készen áll egy minta-alkalmazás és egy bejövő vezérlő üzembe helyezésére az új Kubernetes-infrastruktúrában.

### <a name="setup-kubernetes-credentials"></a>Kubernetes hitelesítő adatainak beállítása
A következő lépésekhez szükség van a Setup [kubectl](https://kubectl.docs.kubernetes.io/) parancsra, amelyet az új Kubernetes-fürthöz való kapcsolódáshoz fogunk használni. [Cloud Shell](https://shell.azure.com/) `kubectl` már telepítve van. A `az` Kubernetes hitelesítő adatainak beszerzéséhez a CLI-t fogjuk használni.

Hitelesítő adatok beolvasása az újonnan üzembe helyezett AK-hoz ([További információ](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>A HRE Pod-identitás telepítése
  Azure Active Directory Pod Identity jogkivonat-alapú hozzáférést biztosít [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md)számára.

  A [HRE Pod Identity](https://github.com/Azure/aad-pod-identity) a következő összetevőket fogja hozzáadni a Kubernetes-fürthöz:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity` , `AzureAssignedIdentity` , `AzureIdentityBinding`
   * [Felügyelt identitás-vezérlő (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) összetevő
   * [Csomópont által felügyelt identitás (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) összetevő


A HRE Pod Identity telepítése a fürtre:

   - *KUBERNETES RBAC engedélyezve* AK-fürt

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *KUBERNETES RBAC letiltva* AK-fürt

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>A Helm telepítése
A [Helm](../aks/kubernetes-helm.md) a Kubernetes csomagkezelő. A csomag telepítéséhez a következőt fogjuk használni `application-gateway-kubernetes-ingress` :

1. Telepítse a [Helm](../aks/kubernetes-helm.md) -t, és futtassa a következő parancsot a Helm-csomag hozzáadásához `application-gateway-kubernetes-ingress` :

    - *KUBERNETES RBAC engedélyezve* AK-fürt

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *KUBERNETES RBAC letiltva* AK-fürt

        ```bash
        helm init
        ```

1. Adja hozzá a AGIC Helm-tárházat:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>A beáramló vezérlő Helm-diagramjának telepítése

1. Használja a `deployment-outputs.json` fent létrehozott fájlt, és hozza létre a következő változókat.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Töltse le a Helm-config. YAML fájlt, amely az AGIC-t konfigurálja:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Vagy másolja az alábbi YAML-fájlt: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Szerkessze az újonnan letöltött Helm-config. YAML, és töltse ki a szakaszt `appgw` és a `armAuth` .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Értékek:
     - `verbosityLevel`: A AGIC naplózási infrastruktúrájának részletességi szintjét állítja be. Lásd: a lehetséges értékek [naplózási szintjei](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) .
     - `appgw.subscriptionId`: Az Azure-előfizetés azonosítója, amelyben Application Gateway található. Például: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Az Azure-erőforráscsoport neve, amelyben a Application Gateway létre lett hozva. Például: `app-gw-resource-group`
     - `appgw.name`: A Application Gateway neve. Például: `applicationgatewayd0f0`
     - `appgw.shared`: Ezt a logikai jelzőt alapértelmezett értékre kell állítani `false` . A beállítás értékeként `true` [megosztott Application Gatewayra](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)van szükség.
     - `kubernetes.watchNamespace`: Adja meg a név területet, amely AGIC kell néznie. Ez lehet egy karakterlánc-érték vagy a névterek vesszővel tagolt listája.
    - `armAuth.type`: lehet `aadPodIdentity` vagy `servicePrincipal`
    - `armAuth.identityResourceID`: Az Azure által felügyelt identitás erőforrás-azonosítója
    - `armAuth.identityClientId`: Az identitás ügyfél-azonosítója. Az identitással kapcsolatos további információkért lásd alább.
    - `armAuth.secretJSON`: Csak akkor szükséges, ha a szolgáltatás egyszerű titkos típusát választotta (ha `armAuth.type` be van állítva `servicePrincipal` ) 


   > [!NOTE]
   > A (z) `identityResourceID` és az `identityClientID` [összetevők telepítése](ingress-controller-install-new.md#deploy-components) lépések során létrehozott értékek, amelyeket a következő parancs használatával lehet megszerezni:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` a fenti parancs a Application Gateway erőforráscsoport. `<identity-name>` a létrehozott identitás neve. Egy adott előfizetéshez tartozó összes identitás a következő használatával listázható: `az identity list`


1. Telepítse a Application Gateway beáramló vezérlő csomagot:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Minta alkalmazás telepítése
Most, hogy Application Gateway, AK és AGIC telepítve van, telepíthetünk egy minta alkalmazást [Azure Cloud Shell](https://shell.azure.com/)használatával:

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

A következőket teheti:

* Töltse le a fenti YAML fájlt:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* A YAML fájl alkalmazása:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Egyéb példák
Ez a [útmutató](ingress-controller-expose-service-over-http-https.md) több példát mutat be arra, hogyan tehet elérhetővé egy AK-szolgáltatást http-n vagy HTTPS-en keresztül az interneten Application Gateway használatával.
