---
title: Beállításjegyzékbeli szerepkörök és engedélyek
description: Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és az identitás-és hozzáférés-kezelés (IAM) használatával részletes engedélyeket biztosíthat az Azure Container Registry erőforrásaihoz.
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 097ccf89caf63d2a504d072cf04c2b534a57a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207954"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Szerepkörök és engedélyek Azure Container Registry

A Azure Container Registry szolgáltatás olyan [beépített Azure-szerepköröket](../role-based-access-control/built-in-roles.md) támogat, amelyek különböző szintű engedélyekkel rendelkeznek az Azure Container registryben. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/index.yml) használatával konkrét engedélyeket rendelhet a felhasználókhoz, a szolgáltatásokhoz, vagy más identitásokhoz, amelyeknek a beállításjegyzékben kell működniük, például lekéréses vagy leküldéses tárolók lemezképeit. [Egyéni szerepköröket](#custom-roles) is meghatározhat részletes engedélyekkel egy beállításjegyzékhez a különböző műveletekhez.

| Szerepkör/engedély       | [Hozzáférés a Resource Managerhez](#access-resource-manager) | [Beállításjegyzék létrehozása/törlése](#create-and-delete-registry) | [Leküldéses rendszerkép](#push-image) | [Lekéréses rendszerkép](#pull-image) | [Rendszerkép-adatok törlése](#delete-image-data) | [Szabályzatok módosítása](#change-policies) |   [Képek aláírása](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Tulajdonos | X | X | X | X | X | X |  |  
| Közreműködő | X | X | X |  X | X | X |  |  
| Olvasó | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

A [szerepkör-hozzárendelés hozzáadásához szükséges lépéseket](../role-based-access-control/role-assignments-steps.md) lásd: a szerepkör-hozzárendelés hozzáadása egy meglévő felhasználóhoz, csoporthoz, egyszerű szolgáltatásnév vagy felügyelt identitáshoz. Használhatja a Azure Portal, az Azure CLI vagy más Azure-eszközöket.

Egyszerű szolgáltatásnév létrehozásakor az Azure-erőforrásokhoz, például egy tároló-beállításjegyzékhez is konfigurálhatja a hozzáférését és engedélyeit. Az Azure CLI-t használó szkriptek esetében lásd: [Azure Container Registry hitelesítés az egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md#create-a-service-principal).

## <a name="differentiate-users-and-services"></a>Felhasználók és szolgáltatások megkülönböztetése

Minden alkalommal érvényesek az engedélyek, a legjobb megoldás az, ha a feladat elvégzéséhez egy személy vagy szolgáltatás számára a legkorlátozottat kell megadni. A következő engedélyek olyan képességeket jelentenek, amelyeket az emberek és a fej nélküli szolgáltatások használhatnak.

### <a name="cicd-solutions"></a>CI/CD-megoldások

`docker build`A CI/CD-megoldásokból származó parancsok automatizálásához képességek szükségesek `docker push` . A fej nélküli szolgáltatási forgatókönyvek esetében javasoljuk, hogy rendelje hozzá a **AcrPush** szerepkört. Ez a szerepkör a szélesebb körben **közreműködő** szerepkörtől eltérően megakadályozza, hogy a fiók más beállításjegyzékbeli műveleteket végezzen, vagy Azure Resource Managerhoz hozzáférjen.

### <a name="container-host-nodes"></a>Tároló-gazdagép csomópontjai

Hasonlóképpen, a tárolókat futtató csomópontoknak a **AcrPull** szerepkörre van szükségük, de nem igényelnek **olvasói** képességeket.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker-bővítmény

Az olyan eszközökhöz, mint a Visual Studio Code [Docker bővítmény](https://code.visualstudio.com/docs/azure/docker), további erőforrás-szolgáltatói hozzáférésre van szükség az elérhető Azure Container-jegyzékek listázásához. Ebben az esetben adja meg a felhasználók hozzáférését az **olvasó** vagy **közreműködő** szerepkörhöz. Ezek a szerepkörök lehetővé teszik a,,, `docker pull` `docker push` `az acr list` `az acr build` és egyéb funkciókat. 

## <a name="access-resource-manager"></a>Hozzáférés a Resource Managerhez

Az [Azure CLI](/cli/azure/)-vel való Azure Portal és beállításjegyzék-kezeléshez Azure Resource Manager hozzáférés szükséges. Ha például a parancs használatával szeretné lekérni a beállításjegyzékek listáját `az acr list` , erre az engedélyre van szüksége. 

## <a name="create-and-delete-registry"></a>Beállításjegyzék létrehozása és törlése

Azure Container-jegyzékek létrehozásának és törlésének lehetősége.

## <a name="push-image"></a>Leküldéses rendszerkép

Egy `docker push` rendszerképet, vagy egy másik [támogatott](container-registry-image-formats.md) összetevő, például egy Helm-diagram leküldését egy beállításjegyzékbe. [Hitelesítés](container-registry-authentication.md) szükséges a beállításjegyzékben a hitelesítő adatokkal. 

## <a name="pull-image"></a>Lekéréses rendszerkép

`docker pull`Nem karanténba helyezett rendszerkép lehetősége, vagy egy másik [támogatott](container-registry-image-formats.md) összetevő, például egy Helm-diagram lekérése egy beállításjegyzékből. [Hitelesítés](container-registry-authentication.md) szükséges a beállításjegyzékben a hitelesítő adatokkal.

## <a name="delete-image-data"></a>Rendszerkép-adatok törlése

Képes törölni a [tároló lemezképeit](container-registry-delete.md), vagy törölhet más [támogatott](container-registry-image-formats.md) összetevőket, például Helm-diagramokat egy beállításjegyzékből.

## <a name="change-policies"></a>Szabályzatok módosítása

Szabályzatok konfigurálása a beállításjegyzékben. A szabályzatok közé tartozik például a rendszerkép kiürítése, a karanténba helyezés és a képaláírások engedélyezése.

## <a name="sign-images"></a>Képek aláírása

Képes a képek aláírására, általában egy automatizált folyamathoz rendelve, amely egy egyszerű szolgáltatást használ. Ez az engedély általában [leküldéses képpel](#push-image) kombinálva lehetővé teszi a megbízható rendszerképek beállításjegyzékbe való leküldését. Részletekért lásd: [a tartalom megbízhatósága Azure Container Registryban](container-registry-content-trust.md).

## <a name="custom-roles"></a>Egyéni szerepkörök

A többi Azure-erőforráshoz hasonlóan a Azure Container Registryhoz részletes engedélyekkel rendelkező [Egyéni szerepkörök](../role-based-access-control/custom-roles.md) is létrehozhatók. Ezután rendelje hozzá az egyéni szerepköröket a felhasználókhoz, a szolgáltatásokhoz, vagy más identitásokhoz, amelyeknek a beállításjegyzékben kell működniük. 

Annak megállapításához, hogy mely engedélyeket kell alkalmazni az egyéni szerepkörre, tekintse meg a Microsoft. ContainerRegistry [műveletek](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)listáját, tekintse át a [beépített ACR-szerepkörök](../role-based-access-control/built-in-roles.md)engedélyezett műveleteit, vagy futtassa a következő parancsot:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Egyéni szerepkör definiálásához tekintse meg az [Egyéni szerepkör létrehozásához szükséges lépéseket](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> Egy egyéni szerepkörben a Azure Container Registry jelenleg nem támogatja a helyettesítő karaktereket, például a `Microsoft.ContainerRegistry/*` vagy `Microsoft.ContainerRegistry/registries/*` az összes egyező művelethez való hozzáférést. A szerepkörben egyenként határozza meg a szükséges műveleteket.

### <a name="example-custom-role-to-import-images"></a>Példa: lemezképek importálására szolgáló egyéni szerepkör

A következő JSON például meghatározza egy egyéni szerepkör minimális műveleteit, amelyek lehetővé teszik a [lemezképek importálását](container-registry-import-images.md) egy beállításjegyzékbe.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

Egyéni szerepkör JSON-Leírás használatával történő létrehozásához vagy frissítéséhez használja az [Azure CLI](../role-based-access-control/custom-roles-cli.md)-t, [Azure Resource Manager sablont](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)vagy más Azure-eszközt. Egyéni szerepkörhöz tartozó szerepkör-hozzárendelések hozzáadása vagy eltávolítása ugyanúgy, mint a beépített Azure-szerepkörökhöz tartozó szerepkör-hozzárendelések kezelése.

## <a name="next-steps"></a>Következő lépések

* További információ az Azure-szerepkörök Azure-identitáshoz való hozzárendeléséről a [Azure Portal](../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../role-based-access-control/role-assignments-cli.md)vagy más Azure-eszközök használatával.

* További információ a Azure Container Registry [hitelesítési lehetőségeiről](container-registry-authentication.md) .

* Ismerje meg, hogyan engedélyezhető az [adattár – hatókörön belüli engedélyek](container-registry-repository-scoped-permissions.md) (előzetes verzió) egy tároló-beállításjegyzékben.
