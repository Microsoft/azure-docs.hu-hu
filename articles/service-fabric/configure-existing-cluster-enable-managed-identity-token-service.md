---
title: Felügyelt identitás támogatásának konfigurálása meglévő Service Fabric-fürtben
description: A következőképpen engedélyezheti a felügyelt identitások támogatását egy meglévő Azure Service Fabric-fürtben
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790039"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Felügyelt identitás támogatásának konfigurálása meglévő Service Fabric-fürtben

Ha a Service Fabric-alkalmazásokban [felügyelt identitásokat](../active-directory/managed-identities-azure-resources/overview.md) szeretne használni az Azure-erőforrásokhoz, először engedélyezze a *felügyelt Identity token szolgáltatást* a fürtön. Ez a szolgáltatás felelős a felügyelt identitások használatával Service Fabric alkalmazások hitelesítéséhez, valamint a hozzáférési jogkivonatok nevében való beszerzéséhez. Ha a szolgáltatás engedélyezve van, a bal oldali ablaktábla **System (rendszer** ) szakaszának Service Fabric Explorer alatt láthatja, hogy a **háló:/System/ManagedIdentityTokenService** néven fut.

> [!NOTE]
> A **felügyelt Identity token szolgáltatás** engedélyezéséhez Service Fabric futtatókörnyezet 6.5.658.9590 vagy újabb verziója szükséges.  
>
> A fürt Service Fabric verzióját a Azure Portal megkeresheti a fürterőforrás megnyitásával és a **Service Fabric Version** tulajdonságának ellenőrzésével az **Essentials (alapvető** erőforrások) szakaszban.
>
> Ha a fürt **manuális** frissítési módban van, először frissítenie kell a 6.5.658.9590 vagy újabb verzióra.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>*Felügyelt identitás-jogkivonat szolgáltatás* engedélyezése meglévő fürtben

Ha engedélyezni szeretné a felügyelt identitás-jogkivonat szolgáltatást egy meglévő fürtben, a fürt frissítését két módosítást kell megadnia: (1) a felügyelt identitási jogkivonat szolgáltatás engedélyezése, valamint (2) az egyes csomópontok újraindításának kérése. Először adja hozzá a következő kódrészletet a fürt Azure Resource Manager sablonhoz:

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

Ahhoz, hogy a módosítások életbe lépjenek, módosítania kell a frissítési házirendet, hogy az egyes csomópontokon a frissítés folyamata során az Service Fabric-futtatókörnyezet kényszerített újraindítását is megadja. Ez az újraindítás biztosítja, hogy az újonnan engedélyezett rendszerszolgáltatás elindult, és minden csomóponton fusson. Az alábbi kódrészletben az `forceRestart` az alapvető beállítás, amely lehetővé teszi az újraindítást. A többi paraméter esetében használja az alább ismertetett értékeket, vagy használja a fürterőforrás számára már megadott meglévő egyéni értékeket. A háló frissítési szabályzatának ("upgradeDescription") egyéni beállításai az Azure Portalon tekinthetők meg, ha a Service Fabric erőforráson vagy a resources.azure.com a "háló frissítése" lehetőséget választja. A frissítési szabályzat ("upgradeDescription") alapértelmezett beállításai nem tekinthetők meg a powershellből vagy a resources.azure.com. További információ: [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) .  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> A frissítés sikeres befejezése után ne felejtse el visszaállítani a `forceRestart` beállítást, hogy csökkentse a további frissítések hatását. 

## <a name="errors-and-troubleshooting"></a>Hibák és hibaelhárítás

Ha az üzembe helyezés a következő üzenettel meghiúsul, az azt jelenti, hogy a fürt nem elég magas Service Fabric-verzión fut:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Következő lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Service Fabric alkalmazás felügyelt identitásának kihasználása a szolgáltatási kódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
