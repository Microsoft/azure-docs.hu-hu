---
title: Az Azure Kubernetes Service (AKS)-fürt hitelesítő adatainak alaphelyzetbe állítása
description: Ismerje meg, hogyan frissítés vagy visszaállítása az egyszerű szolgáltatás hitelesítő adatait egy fürtöt az Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/30/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: d880615d0d132403c935fe39e8478d7b3fc48dbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029359"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Frissítés vagy a hitelesítő adatok forgatása egyszerű szolgáltatások Azure Kubernetes Service (AKS)

Alapértelmezés szerint az AKS-fürtök jönnek létre, amely egy éves lejárati ideje szolgáltatásnévvel. Az Ön közelében a lejárati dátum alaphelyzetbe állíthatja a bővítése az egyszerű szolgáltatás egy további időszakban a hitelesítő adatokat. Érdemes azt is, frissítéséhez vagy, egy meghatározott biztonsági szabályzat részeként a hitelesítő adatok forgatása. Ez a cikk részletesen bemutatja ezeket a hitelesítő adatokat az AKS-fürt frissítéséhez.

## <a name="before-you-begin"></a>Előzetes teendők

Az Azure CLI 2.0.56 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Válassza ki a frissíteni vagy egyszerű szolgáltatás létrehozása

Ha meg szeretné frissíteni az AKS-fürt hitelesítő adatait, a választhat:

* Frissítse a hitelesítő adatokat a meglévő szolgáltatásnévhez a fürt által használt vagy
* Hozzon létre egy egyszerű szolgáltatást, és frissítse a fürt ezen új hitelesítő adatokkal.

Ha azt szeretné, hozzon létre egy egyszerű szolgáltatást az AKS-fürt frissítése, hagyja ki az a jelen szakaszban ismertetett lépések és továbbra is [egyszerű szolgáltatás létrehozása](#create-a-service-principal). Ha szeretné frissíteni a hitelesítő adatait a meglévő egyszerű szolgáltatást az AKS-fürt által használt, folytassa a jelen szakaszban ismertetett lépéseket.

### <a name="get-the-service-principal-id"></a>A résztvevő-azonosító beszerzése

A létező egyszerű szolgáltatás hitelesítő adatainak frissítéséhez, kérje le a szolgáltatásnév-Azonosítót, a fürt használata a [az aks show] [ az-aks-show] parancsot. Az alábbi példa lekéri a fürt nevű azonosítója *myAKSCluster* a a *myResourceGroup* erőforráscsoportot. A szolgáltatásnév-Azonosítót további parancs használható változóként van megadva.

```azurecli
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>A szolgáltatásnév hitelesítő adatainak frissítése

A változó beállítása, amely tartalmazza a szolgáltatásnév-Azonosítót, állítsa alaphelyzetbe a hitelesítő adatok használatával [az ad sp hitelesítő adatok alaphelyzetbe állítása][az-ad-sp-credential-reset]. Az alábbi példa az Azure-biztonságos új titkos kód létrehozása a szolgáltatásnévhez tartozó platform lehetővé teszi. Az új biztonságos titkos kulcsot is egy változóban van tárolva.

```azurecli
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Most már továbbléphet a következőre [AKS-fürt frissítése új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ha úgy döntött, hogy frissítse a meglévő egyszerű szolgáltatás hitelesítő adatai az előző szakaszban, kihagyhatja ezt a lépést. Továbbra is [AKS-fürt frissítése új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials).

Hozzon létre egy egyszerű szolgáltatást, és frissítse az AKS-fürt új ezekkel a hitelesítő adatokkal, használja a [az ad sp create-for-rbac] [ az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. Ezekkel az értékekkel a következő lépésben.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Ezután határozza meg a szolgáltatás egyszerű azonosító és a titkos ügyfélkódra kimenetét a saját használatával változóival [az ad sp create-for-rbac] [ az-ad-sp-create] parancsot, az alábbi példában látható módon. A *SP_ID* van a *appId*, és a *SP_SECRET* van a *jelszó*:

```azurecli
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>AKS-fürt frissítése új hitelesítő adatokkal

Függetlenül attól, hogy úgy döntött, hogy a létező egyszerű szolgáltatás hitelesítő adatainak frissítéséhez, vagy hozzon létre egy egyszerű szolgáltatást, most frissítenie az AKS-fürtöt az új hitelesítő adatok használatával a [az aks-hitelesítő adatok frissítése] [ az-aks-update-credentials] parancsot. Változói a *– egyszerű szolgáltatás* és *--client-secret* használhatók:

```azurecli
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Az egyszerű szolgáltatás hitelesítő adatai az AKS frissíteni kell néhány másodpercig tart.

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS-fürthöz tartozó egyszerű szolgáltatás frissítve lett. Identitáskezelés a fürtön belüli számítási feladatokhoz való további információkért lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-aks-update-credentials]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: https://docs.azure.cn/zh-cn/cli/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: https://docs.azure.cn/zh-cn/cli/ad/sp/credential?view=azure-cli-latest#az-ad-sp-credential-reset