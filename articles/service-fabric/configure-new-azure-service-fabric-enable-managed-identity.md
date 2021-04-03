---
title: Felügyelt identitás támogatásának konfigurálása új Service Fabric-fürthöz
description: A következőképpen engedélyezheti a felügyelt identitások támogatását egy új Azure Service Fabric-fürtben
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574666"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Felügyelt identitás támogatásának konfigurálása új Service Fabric-fürthöz

Ha a Service Fabric-alkalmazásokban [felügyelt identitásokat](../active-directory/managed-identities-azure-resources/overview.md) szeretne használni az Azure-erőforrásokhoz, először engedélyezze a *felügyelt Identity token szolgáltatást* a fürtön. Ez a szolgáltatás felelős a felügyelt identitások használatával Service Fabric alkalmazások hitelesítéséhez, valamint a hozzáférési jogkivonatok nevében való beszerzéséhez. Ha a szolgáltatás engedélyezve van, a bal oldali ablaktábla **System (rendszer** ) szakaszának Service Fabric Explorer alatt láthatja, hogy az egyéb rendszerszolgáltatások mellett a **háló:/System/ManagedIdentityTokenService** néven fut.

> [!NOTE]
> A **felügyelt Identity token szolgáltatás** engedélyezéséhez Service Fabric futtatókörnyezet 6.5.658.9590 vagy újabb verziója szükséges.  

## <a name="enable-the-managed-identity-token-service"></a>Felügyelt Identity token szolgáltatás engedélyezése

Ha engedélyezni szeretné a felügyelt identitás-jogkivonat szolgáltatást a fürt létrehozási idején, adja hozzá a következő kódrészletet a fürthöz Azure Resource Manager sablonhoz:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Hibák

Ha az üzembe helyezés ezzel az üzenettel meghiúsul, az azt jelenti, hogy a fürt nincs a szükséges Service Fabric-verzióban (a minimális támogatott futtatókörnyezet 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Felügyelt identitások támogatásának](./concepts-managed-identity.md) áttekintése az Azure Service Fabric

* [Felügyelt identitás támogatásának engedélyezése meglévő Azure Service Fabric-fürtben](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Következő lépések

* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Service Fabric alkalmazás felügyelt identitásának kihasználása a szolgáltatási kódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)