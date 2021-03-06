---
title: Bevezető szolgáltatásnév létrehozása az Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Azure arc-kompatibilis bevezető szolgáltatás létrehozása '
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: 610b355073473f0e492350753a523b7943666f13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121745"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Bevezető szolgáltatásnév létrehozása az Azure arc-kompatibilis Kubernetes

## <a name="overview"></a>Áttekintés

A Kubernetes-fürtöket az Azure arc szolgáltatáshoz a korlátozott jogosultságú szerepkör-hozzárendelésekkel rendelkező egyszerű szolgáltatások használatával lehet összekötni. Ez a képesség a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) folyamatokban, például az Azure-folyamatokban és a GitHub-műveletekben hasznos.

Az alábbi lépésekkel megtudhatja, hogyan használhatja a Kubernetes-fürtöket az Azure arc szolgáltatáshoz kapcsolódó egyszerű szolgáltatásokat.

## <a name="create-a-new-service-principal"></a>Új egyszerű szolgáltatás létrehozása

Hozzon létre egy új egyszerű szolgáltatásnevet a Azure Active Directory bérlő számára egyedi tájékoztató névvel.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Kimeneti**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Engedélyek hozzárendelése

Rendelje hozzá a "Kubernetes-fürt-Azure arc bevezetése" szerepkört az újonnan létrehozott egyszerű szolgáltatáshoz. Ez a beépített Azure-szerepkör korlátozott engedélyekkel rendelkezik, így a rendszerbiztonsági tag csak fürtöket regisztrálhat az Azure-ba. A hozzárendelt szerepkörrel rendelkező rendszerbiztonsági tag nem tudja frissíteni, törölni vagy módosítani az előfizetésen belüli többi fürtöt és erőforrást.

A korlátozott képességek miatt az ügyfelek könnyedén újra használhatják ezt a résztvevőt több fürt bevezetéséhez.

Az engedélyek további korlátozásához adja meg a megfelelő `--scope` argumentumot a szerepkör hozzárendeléséhez. Ez lehetővé teszi a rendszergazdák számára, hogy korlátozzák a fürt regisztrációját az előfizetés vagy az erőforráscsoport hatóköre számára. A különböző paraméterek a következő forgatókönyveket támogatják `--scope` :

| Erőforrás  | `scope` argumentum| Hatás |
| ------------- | ------------- | ------------- |
| Előfizetés | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Az egyszerű szolgáltatás minden olyan erőforráscsoport számára regisztrálhatja a fürtöt, amely az előfizetés alatt található. |
| Erőforráscsoport | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Az egyszerű szolgáltatásnév __csak__ a fürtöket regisztrálja az erőforráscsoporthoz `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Kimeneti**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Egyszerű szolgáltatásnév használata az Azure CLI-vel

Az újonnan létrehozott szolgáltatásnevet az alábbi parancsokkal hivatkozhat:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Következő lépések

A fürt konfigurációjának szabályozása [Azure Policy használatával](./use-azure-policy.md).
