---
title: Tárolók összeállítása, tesztelése és üzembe helyezése Azure Kubernetes Service a GitHub Actions
description: Megtudhatja, hogyan használhatja GitHub Actions a tároló Kubernetesben való üzembe helyezéséhez
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3d0b6030cc63d0d7f4eac2c72c3545cf315b1fd3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832386"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions Kubernetes Service-be való üzembe helyezéshez

[GitHub Actions](https://docs.github.com/en/actions) lehetővé teszi egy automatizált szoftverfejlesztési életciklus-munkafolyamat felépítésének rugalmasságát. Több Kubernetes-művelettel is üzembe helyezhet tárolókat a Azure Container Registry- és Azure Kubernetes Service-GitHub Actions. 

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nem tudja, regisztráljon [ingyenesen.](https://github.com/join)  
- Működő Kubernetes-fürt
    - [Oktatóanyag: Alkalmazás előkészítése Azure Kubernetes Service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Munkafolyamat-fájl áttekintése

A munkafolyamatot egy YAML- (.yml-) fájl definiálja az `/.github/workflows/` adattár elérési útján. Ez a definíció tartalmazza a munkafolyamatot felhozó különböző lépéseket és paramétereket.

Az AKS-t célzó munkafolyamatok fájlja három szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | Bejelentkezés privát tároló-beállításjegyzékbe (ACR) |
|**Létrehozás** | Build & tároló rendszerképének leküldése  |
|**Telepítés** | 1. A cél AKS-fürt beállítása |
| |2. Általános/docker-registry titkos kulcs létrehozása a Kubernetes-fürtben  |
||3. Üzembe helyezés a Kubernetes-fürtön|

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Szolgáltatásnév [létrehozásához](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) használja az [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) parancsot az [Azure CLI-n.](/cli/azure/) Ezt a parancsot [futtathatja](https://shell.azure.com/) a Azure Cloud Shell a Azure Portal a Próbálja **ki gombot** választva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

A fenti parancsban cserélje le a helyőrzőket az előfizetés-azonosítójára és az erőforráscsoportra. A kimenet a szerepkör-hozzárendelési hitelesítő adatok, amelyek hozzáférést biztosítanak az erőforráshoz. A parancs kimenete egy ehhez hasonló JSON-objektum lesz.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Másolja ezt a JSON-objektumot, amellyel a GitHubról hitelesíthet.

## <a name="configure-the-github-secrets"></a>A GitHub titkos kód konfigurálása

Kövesse a titkos kulcsok konfigurálásának lépéseit:

1. A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Settings > Secrets > Add a new secret (Titkos kulcsok hozzáadása) **lehetőséget.**

    ![Képernyőkép az Új titkos adattár hozzáadása hivatkozásról.](media/kubernetes-action/secrets.png)

2. Illessze be a fenti `az cli` parancs tartalmát a titkos változó értékeként. Például: `AZURE_CREDENTIALS`.

3. Hasonlóképpen határozza meg a következő további titkos kulcsokat a tároló-beállításjegyzék hitelesítő adataihoz, és állítsa be őket a Docker bejelentkezési műveletében. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. A titkos kulcsok az alább látható módon jelennek meg, amint definiálva van.

    ![Az adattár meglévő titkosait bemutató képernyőkép.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tárolólemezkép létrehozása és üzembe helyezése Azure Kubernetes Service fürtön

A tároló rendszerképének összeállítása és leküldése művelet használatával `Azure/docker-login@v1` történik. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Üzembe helyezés Azure Kubernetes Service fürtön

Tároló rendszerképének AKS-hez való üzembe helyezéséhez a műveletet kell `Azure/k8s-deploy@v1` használnia. Ennek a műveletnek öt paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **Névtér** | (Nem kötelező) Válassza ki a cél Kubernetes-névteret. Ha a névtér nincs megjelölve, a parancsok az alapértelmezett névtérben fognak futni | 
| **Rakományjegyzék** |  (Kötelező) Az üzembe helyezéshez használt jegyzékfájlok elérési útja |
| **Képek** | (Nem kötelező) A jegyzékfájlok helyettesítésére használt kép(ek) teljes erőforrás-URL-címe |
| **imagepullsecrets (képpullsecrets)** | (Nem kötelező) A fürtön belül már beállított docker-registry titkos kulcs neve. Mindegyik titkos név hozzá lesz adva az imagePullSecrets mező alatt a bemeneti jegyzékfájlokban található számítási feladatokhoz |
| **kubectl-version** | (Nem kötelező) Telepíti a kubectl binary egy adott verzióját |


Az AKS-hez való üzembe helyezés előtt be kell állítania a cél Kubernetes-névteret, és létre kell hoznia egy rendszerkép lekért titkos nevét. A [rendszerképek lehúzásával](../container-registry/container-registry-auth-kubernetes.md)kapcsolatos további információkért lásd: Rendszerképek lehúzása Egy Azure-beli tároló-beállításjegyzékből egy Kubernetes-fürtbe. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      arguments: --force true
```


Az üzembe helyezés befejezéséhez a műveletet `k8s-deploy` kell végrehajtania. Cserélje le a környezeti változókat az alkalmazás értékeire. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a Kubernetes-fürtre, a tárolójegyzékre és az adattárra már nincs szükség, törölje az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-adattár törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>További Kubernetes-GitHub Actions

* [Kubectl-eszköz telepítője](https://github.com/Azure/setup-kubectl) ( ): A kubectl egy adott verzióját telepíti `azure/setup-kubectl` a futón.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context) ( ): A cél Kubernetes-fürt környezetének beállítása, amelyet más műveletek fognak használni, vagy bármilyen `azure/k8s-set-context` kubectl-parancs futtatása.
* [AKS set context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): A célként Azure Kubernetes Service fürtkörnyezetben.
* [Kubernetes create secret](https://github.com/Azure/k8s-create-secret) ( ): Hozzon létre egy általános titkos adatokat vagy `azure/k8s-create-secret` docker-registry titkos adatokat a Kubernetes-fürtben.
* [Kubernetes deploy](https://github.com/Azure/k8s-deploy) ( ): Jegyzékfájlokat sikkeszthet és helyezhet üzembe `azure/k8s-deploy` a Kubernetes-fürtökön.
* [Setup Helm](https://github.com/Azure/setup-helm) ( ): Telepítse a Helm bináris fájl egy adott verzióját `azure/setup-helm` a futón.
* [Kubernetes bake](https://github.com/Azure/k8s-bake) ( ): A helm2, kustomize vagy kompose használatával üzembe helyezéshez használt `azure/k8s-bake` jegyzékfájl beépítése.
* [Kubernetes lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): Ellenőrizze/lint a jegyzékfájlokat.
