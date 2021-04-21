---
title: Fürt hitelesítő adatainak alaphelyzetbe állítása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan frissítheti vagy állíthatja alaphelyzetbe a szolgáltatásnév vagy az AAD-alkalmazás hitelesítő adatait egy Azure Kubernetes Service (AKS-) fürthöz
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 0b750eb9af7dfd7bcbada7500b6ef71b015db11f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767474"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>A hitelesítő adatok frissítése vagy Azure Kubernetes Service (AKS)

A szolgáltatásnévvel létrehozott AKS-fürtök lejárati ideje egy év. Ahogy közeledik a lejárati dátumhoz, alaphelyzetbe állíthatja a hitelesítő adatokat a szolgáltatásnév további időtartamra való meghosszabbítására. A hitelesítő adatokat egy meghatározott biztonsági szabályzat részeként is frissítheti vagy forgathatja. Ez a cikk részletesen bemutatja, hogyan frissítheti ezeket a hitelesítő adatokat egy AKS-fürthöz.

Előfordulhat, hogy az [AKS-fürtöt][aad-integration]a Azure Active Directory integrálta, és hitelesítésszolgáltatóként használja a fürthöz. Ebben az esetben további 2 identitás lesz létrehozva a fürthöz, az AAD Server alkalmazás és az AAD-ügyfélalkalmazás, ezeket a hitelesítő adatokat is alaphelyzetbe állíthatja.

Másik lehetőségként felügyelt identitást is használhat az engedélyekhez szolgáltatásnév helyett. A felügyelt identitások kezelése egyszerűbb, mint a szolgáltatásnév, és nincs szükség frissítésekre vagy rotációkra. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.65-ös vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Új szolgáltatásnév frissítése vagy létrehozása az AKS-fürthöz

Ha frissíteni szeretné egy AKS-fürt hitelesítő adatait, a következő két lehetőség közül választhat:

* Frissítse a meglévő szolgáltatásnév hitelesítő adatait.
* Hozzon létre egy új szolgáltatásnév, és frissítse a fürtöt az új hitelesítő adatok használatára. 

> [!WARNING]
> Ha új szolgáltatásnév  létrehozása között dönt, a nagy méretű AKS-fürt ezen hitelesítő adatok használatára való frissítése hosszú időt vehet igénybe.

### <a name="check-the-expiration-date-of-your-service-principal"></a>A szolgáltatásnév lejárati dátumának ellenőrzése

A szolgáltatásnév lejárati dátumának ellenőrzéshez használja [az az ad sp credential list][az-ad-sp-credential-list] parancsot. Az alábbi példa lekérte a *myResourceGroup erőforráscsoportban* a *myAKSCluster* nevű fürt egyszerű szolgáltatásazonosítóját [az az aks show paranccsal.][az-aks-show] A szolgáltatásnév-azonosító egy *SP_ID* nevű változóként van beállítva, amely az [az ad sp credential list paranccsal használható.][az-ad-sp-credential-list]

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>A meglévő szolgáltatásnév hitelesítő adatainak alaphelyzetbe állítása

A meglévő szolgáltatásnév hitelesítő adatainak frissítéséhez az [az aks show][az-aks-show] paranccsal szerezze be a fürt egyszerű szolgáltatásazonosítóját. A következő példa lekérte a *myResourceGroup erőforráscsoportban* a *myAKSCluster* nevű fürt azonosítóját. A szolgáltatásnév-azonosító egy SP_ID nevű *változóként* van beállítva, amely további parancsban használható. Ezek a parancsok Bash-szintaxist használnak.

> [!WARNING]
> Amikor alaphelyzetbe állítja a fürt hitelesítő adatait egy Azure Virtual Machine Scale Sets-t használó AKS-fürtön, [a][node-image-upgrade] csomópontok rendszerképének frissítésével frissíti a csomópontokat az új hitelesítő adatokkal.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

A szolgáltatásnév azonosítóját tartalmazó változókészlet esetén most állítsa alaphelyzetbe a hitelesítő adatokat [az az ad sp credential reset használatával.][az-ad-sp-credential-reset] Az alábbi példa lehetővé teszi, hogy az Azure platform új titkos adatokat hozzon létre a szolgáltatásnévhez. Az új biztonságos titkos adatokat a rendszer változóként is tárolja.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Most frissítse az [AKS-fürtöt az új szolgáltatásnév hitelesítő adataival.](#update-aks-cluster-with-new-service-principal-credentials) Erre a lépésre azért van szükség, hogy a szolgáltatásnév módosításai tükrözzék az AKS-fürtöt.

### <a name="create-a-new-service-principal"></a>Új szolgáltatásnév létrehozása

Ha úgy döntött, hogy frissíti a meglévő szolgáltatásnév hitelesítő adatait az előző szakaszban, hagyja ki ezt a lépést. Folytassa az [AKS-fürt frissítését az új szolgáltatásnév hitelesítő adataival.](#update-aks-cluster-with-new-service-principal-credentials)

Szolgáltatásnév létrehozásához, majd az AKS-fürt ezen új hitelesítő adatokkal való frissítéséhez használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. Ezeket az értékeket a következő lépésben használjuk.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Most határozzon meg változókat a szolgáltatásnév azonosítójára és a titkos ügyfél titkos kódjára a saját [az ad sp create-for-rbac][az-ad-sp-create] parancs kimenetével, az alábbi példában látható módon. A *SP_ID* az *appId,* a SP_SECRET *pedig* az Ön *jelszava:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Most pedig frissítse az [AKS-fürtöt az új szolgáltatásnév hitelesítő adataival.](#update-aks-cluster-with-new-service-principal-credentials) Ez a lépés szükséges ahhoz, hogy a szolgáltatásnév módosításai tükrözzék az AKS-fürtöt.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Az AKS-fürt frissítése új szolgáltatásnév hitelesítő adataival

> [!IMPORTANT]
> Nagy fürtök esetén az AKS-fürt új szolgáltatásnévvel való frissítése hosszú időt vehet igénybe.

Függetlenül attól, hogy úgy döntött, frissíti a meglévő szolgáltatásnév hitelesítő adatait, vagy létrehoz egy szolgáltatásnévt, most az [az aks update-credentials][az-aks-update-credentials] paranccsal frissíti az AKS-fürtöt az új hitelesítő adataival. A *--service-principal* és *--client-secret változók* a következőt használják:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

A kis és közepes méretű fürtök esetén néhány másodpercet vesz igénybe, hogy a szolgáltatásnév hitelesítő adatai frissülnek az AKS-ban.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>AKS-fürt frissítése az AAD-alkalmazás új hitelesítő adataival

Az AAD-integráció lépéseit követve új AAD-kiszolgáló- és ügyfélalkalmazásokat [hozhat létre.][create-aad-app] Vagy állítsa alaphelyzetbe a meglévő AAD-alkalmazásokat ugyanazokkal a metódusokkal, mint a [szolgáltatásnév alaphelyzetbe állítása esetén.](#reset-the-existing-service-principal-credential) Ezt követően csak frissítenie kell a fürt AAD-alkalmazás hitelesítő adatait ugyanazokkal az [az aks update-credentials paranccsal,][az-aks-update-credentials] de a *--reset-aad változókat* kell használnia.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben magát az AKS-fürtöt és az AAD-integrációs alkalmazásokat is frissítettük. További információ a fürtön belüli számítási feladatok identitásának kezeléséhez: Ajánlott eljárások a hitelesítéshez és engedélyezéshez az [AKS-ban.][best-practices-identity]

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md
