---
title: Tárolók létrehozása, tesztelése és üzembe helyezése az Azure Kubernetes Service-ben GitHub-műveletek használatával
description: Ismerje meg, hogyan helyezhet üzembe a tárolót a GitHub-műveletek használatával a Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 94134360de49a066f825cbb0c85712995d90b37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761452"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>A Kubernetes szolgáltatás üzembe helyezéséhez szükséges GitHub-műveletek

A [GitHub-műveletek](https://docs.github.com/en/actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztési életciklus-munkafolyamatot. Több Kubernetes műveletet is használhat a Azure Container Registry tárolók számára az Azure Kubernetes Service-be a GitHub-műveletekkel való üzembe helyezéshez. 

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).  
- Egy működő Kubernetes-fürt
    - [Oktatóanyag: alkalmazás előkészítése az Azure Kubernetes Service-hez](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

Az AK-t tartalmazó munkafolyamatok esetében a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | Bejelentkezés a Private Container registrybe (ACR) |
|**Létrehozás** | Build & a tároló rendszerképének leküldése  |
|**Telepítés** | 1. a cél AK-fürt beállítása |
| |2. hozzon létre egy általános/Docker-beállításjegyzékbeli titkot a Kubernetes-fürtben  |
||3. üzembe helyezés a Kubernetes-fürtön|

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com/) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

A fenti parancsban cserélje le a helyőrzőket az előfizetés-azonosítójával és az erőforráscsoporthoz. A kimenet a szerepkör-hozzárendelés hitelesítő adatai, amelyek hozzáférést biztosítanak az erőforráshoz. A parancsnak a következőhöz hasonló JSON-objektumot kell kiadnia.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Másolja ezt a JSON-objektumot, amelyet a GitHubról történő hitelesítéshez használhat.

## <a name="configure-the-github-secrets"></a>A GitHub-titkok konfigurálása

A titkok konfigurálásához kövesse a következő lépéseket:

1. A [githubon](https://github.com/)tallózással keresse meg a tárházat, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

    ![Képernyőfelvétel: az új titkos kulcs hozzáadása egy adattárhoz.](media/kubernetes-action/secrets.png)

2. Illessze be a fenti parancs tartalmát a `az cli` titkos változó értékére. Például: `AZURE_CREDENTIALS`.

3. Hasonlóképpen adja meg a következő további titkokat a tároló beállításjegyzékbeli hitelesítő adataihoz, és állítsa be őket a Docker bejelentkezési műveletben. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. A titkokat az alább látható módon fogja látni.

    ![Képernyőfelvétel: egy adattár meglévő titkait jeleníti meg.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tároló lemezképének létrehozása és üzembe helyezése az Azure Kubernetes Service-fürtön

A tároló lemezképek felépítésének és leküldésének lépései a művelet használatával hajthatók végre `Azure/docker-login@v1` . 


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

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Üzembe helyezés az Azure Kubernetes Service-fürtben

Ha a Container-lemezképet AK-ba szeretné telepíteni, akkor a műveletet kell használnia `Azure/k8s-deploy@v1` . Ehhez a művelethez öt paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **névtér** | Választható Válassza ki a cél Kubernetes-névteret. Ha a névtér nincs megadva, a parancsok az alapértelmezett névtérben fognak futni. | 
| **jegyzékek** |  Szükséges A jegyzékfájl elérési útja, amelyet a rendszer az üzembe helyezéshez fog használni |
| **képek** | Választható A jegyzékfájlok helyettesítéséhez használni kívánt rendszerkép (ek) teljes erőforrás-URL-címe |
| **imagepullsecrets** | Választható Egy olyan Docker-beállításjegyzékbeli titok neve, amely már be van állítva a fürtön belül. Az egyes titkos nevek a imagePullSecrets mezőben találhatók a bemeneti jegyzékfájlban található munkaterhelések esetében. |
| **kubectl – verzió** | Választható A kubectl bináris fájl egy adott verzióját telepíti. |


Az AK-ra való üzembe helyezés előtt be kell állítania a TARGET Kubernetes-névteret, és létre kell hoznia egy rendszerkép-lekérési titkot. A képek húzásának működéséről további információt a [képek lekérése egy Azure Container registryből egy Kubernetes-fürtre](../container-registry/container-registry-auth-kubernetes.md)című témakörben talál. 

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
      force: true
```


Fejezze be a telepítést a `k8s-deploy` művelettel. Cserélje le a környezeti változókat az alkalmazás értékeire. 

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

Ha a Kubernetes-fürt, a tároló-beállításjegyzék és a tárház már nem szükséges, a telepített erőforrások tisztításához törölje az erőforráscsoportot és a GitHub-tárházat. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure Kubernetes szolgáltatásról](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>További Kubernetes GitHub-műveletek

* [Kubectl Tool Installer](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): a Kubectl adott verzióját telepíti a futón.
* [Kubernetes set Context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): állítsa be azt a cél Kubernetes-fürtöt, amelyet más műveletek használni fognak, vagy futtasson bármely kubectl-parancsot.
* [AK set Context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): állítsa be a cél Azure Kubernetes szolgáltatás-fürt környezetét.
* [Kubernetes Create Secret](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): hozzon létre egy általános titkos vagy Docker-beállításjegyzékbeli titkot a Kubernetes-fürtben.
* [Kubernetes üzembe helyezése](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): a Kubernetes-fürtökhöz tartozó jegyzékfájlok sütni és üzembe helyezése.
* [Setup Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): telepítse a Helm bináris egy adott verzióját a futóra.
* [Kubernetes Bake](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): a helm2, kustomize vagy kompose használatával üzemelő példányokhoz használandó manifest-fájl.
* [Kubernetes](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): a jegyzékfájlok érvényességének ellenőrzése/beadása.
