---
title: Integrációs szolgáltatási környezetek (ISEs) létrehozása Logic Apps REST API
description: Hozzon létre egy integrációs szolgáltatási környezetet (ISE) a Logic Apps REST API használatával, hogy elérhető legyen az Azure Virtual Networks (virtuális hálózatok) az Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550657"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps REST API-val

Olyan esetekben, amikor a Logic apps és az integrációs fiókok hozzáférést igényelnek egy Azure-beli [virtuális hálózathoz](../virtual-network/virtual-networks-overview.md), létrehozhat egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a Logic apps REST API használatával. További információ a ISEs: [Azure Virtual Network-erőforrások elérése Azure Logic Appsból](connect-virtual-network-vnet-isolated-environment-overview.md).

Ez a cikk bemutatja, hogyan hozhat létre egy ISE-t a Logic Apps REST API általános használatával. Szükség esetén engedélyezheti a [rendszerhez rendelt vagy felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) is az ISE-ben, de csak az Logic apps REST API használatával. Ez az identitás lehetővé teszi, hogy az ISE hitelesítse a biztonságos erőforrásokhoz, például a virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz való hozzáférést, amelyek egy Azure-beli virtuális hálózathoz tartoznak vagy kapcsolódnak. Így nem kell bejelentkeznie a hitelesítő adataival.

Az ISE további létrehozási módjaival kapcsolatos további információkért tekintse meg a következő cikkeket:

* [ISE létrehozása a Azure Portal használatával](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [ISE létrehozása a minta Azure Resource Manager rövid útmutató sablon használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Hozzon létre egy olyan ISE-t, amely támogatja az ügyfél által felügyelt kulcsokat a REST-adatok titkosításához](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Előfeltételek

* Ugyanazokat az [előfeltételeket](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) és [hozzáférési követelményeket](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) kell meghoznia, mint amikor egy ISE-t hoz létre a Azure Portal

* Az ISE-ben használni kívánt további erőforrások, hogy az ISE-definícióban is szerepeljenek az adatai, például: 

  * Az önaláírt tanúsítványok támogatásának engedélyezéséhez meg kell adnia a tanúsítványra vonatkozó információkat az ISE-definícióban.

  * A felhasználó által hozzárendelt felügyelt identitás engedélyezéséhez előbb létre kell hoznia ezt az identitást, és tartalmaznia kell a és a `objectId` `principalId` `clientId` tulajdonságokat és azok ÉRTÉKeit az ISE-definícióban. További információ: [felhasználó által hozzárendelt felügyelt identitás létrehozása a Azure Portalban](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Egy eszköz, amellyel létrehozhatja az ISE-t úgy, hogy meghívja a Logic Apps REST API egy HTTPS PUT-kéréssel. Használhatja például a [Poster](https://www.getpostman.com/downloads/)-t, vagy létrehozhat egy logikai alkalmazást, amely elvégzi ezt a feladatot.

## <a name="create-the-ise"></a>Az ISE létrehozása

Az ISE létrehozásához a Logic Apps REST API meghívásával végezze el ezt a HTTPS PUT-kérelmet:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A Logic Apps REST API 2019-05-01-es verziójának a saját HTTP PUT-kérelmét kell használnia az ISE-összekötők számára.

Az üzembe helyezés általában két órán belül befejeződik. Alkalmanként az üzembe helyezés akár négy órát is igénybe vehet. A központi telepítés állapotának megtekintéséhez a [Azure Portal](https://portal.azure.com)az Azure eszköztárán válassza az értesítések ikont, amely megnyitja az értesítések panelt.

> [!NOTE]
> Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure akár egy órát is igénybe vehet az alhálózatok felszabadítása előtt. Ez azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználja ezeket az alhálózatokat egy másik ISE-ben.
>
> Ha törli a virtuális hálózatot, az Azure általában akár két órával az alhálózatok felszabadítása előtt is eltarthat, de ez a művelet hosszabb időt is igénybe vehet. 
> A virtuális hálózatok törlésekor győződjön meg arról, hogy egyetlen erőforrás sincs még csatlakoztatva. 
> Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Kérelem fejléce

A kérelem fejlécében adja meg a következő tulajdonságokat:

* `Content-type`: Állítsa ezt a tulajdonságot értékre `application/json` .

* `Authorization`: Állítsa ezt a tulajdonságot annak az ügyfélnek a tulajdonosi jogkivonatára, aki hozzáfér a használni kívánt Azure-előfizetéshez vagy erőforráscsoporthoz.

<a name="request-body"></a>

## <a name="request-body"></a>A kérés törzse

A kérelem törzsében adja meg az ISE létrehozásához használandó erőforrás-definíciót, beleértve az ISE-ben engedélyezni kívánt további funkciókra vonatkozó információkat is, például:

* Ha olyan ISE-t szeretne létrehozni, amely lehetővé teszi egy, a helyen telepített vállalati hitelesítésszolgáltató által kiadott önaláírt tanúsítvány és tanúsítvány használatát `TrustedRoot` , vegye fel az `certificates` OBJEKTUMOT az ISE definíciójának `properties` szakaszán belül, ahogy ezt a cikket később ismertetjük.

* A rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitást használó ISE létrehozásához adja `identity` meg az objektumot a felügyelt identitás típusával és az egyéb szükséges információkkal az ISE-definícióban, ahogy ezt a cikket később ismertetjük.

* Az ügyfél által felügyelt kulcsokat és Azure Key Vaultokat használó ISE az inaktív adatok titkosításához való létrehozásához adja meg az [ügyfél által felügyelt kulcs támogatását lehetővé tevő adatokat](customer-managed-keys-integration-service-environment.md). Az ügyfél által felügyelt kulcsokat *csak a létrehozáskor* állíthatja be, később nem.

### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa

Itt látható a kérelem törzsének szintaxisa, amely leírja az ISE létrehozásakor használandó tulajdonságokat:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Példa a kérelem szövegtörzsére

A példaként szolgáló kérelem törzse a következő minta értékeket jeleníti meg:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Egyéni főtanúsítványok hozzáadása

Gyakran egy ISE használatával kapcsolódhat az egyéni szolgáltatásokhoz a virtuális hálózaton vagy a helyszínen. Ezeket az egyéni szolgáltatásokat gyakran egy egyéni legfelső szintű hitelesítésszolgáltató által kiadott tanúsítvány védi, például egy vállalati hitelesítésszolgáltató vagy egy önaláírt tanúsítvány. További információ az önaláírt tanúsítványok használatáról: [biztonságos hozzáférés és adathozzáférés a kimenő hívások számára más szolgáltatásokhoz és rendszerekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Ahhoz, hogy az ISE sikeresen csatlakozhasson ezekhez a szolgáltatásokhoz Transport Layer Security (TLS) protokollon keresztül, az ISE-nek hozzá kell férnie ezekhez a főtanúsítványokhoz.

#### <a name="considerations-for-adding-custom-root-certificates"></a>Egyéni főtanúsítványok hozzáadásával kapcsolatos megfontolások

Mielőtt frissíti az ISE-t egy egyéni megbízható főtanúsítvánnyal, tekintse át a következő szempontokat:

* Ügyeljen arra, hogy feltöltse a főtanúsítványt *és* az összes köztes tanúsítványt. A tanúsítványok maximális száma 20.

* A főtanúsítványok feltöltése olyan helyettesítő művelet, amelyben a legújabb feltöltés felülírja a korábbi feltöltéseket. Ha például olyan kérelmet küld, amely feltölt egy tanúsítványt, majd egy másik kérést küld egy másik tanúsítvány feltöltésére, az ISE csak a második tanúsítványt használja. Ha mindkét tanúsítványt használni szeretné, vegye fel őket együtt ugyanabban a kérelemben.  

* A főtanúsítványok feltöltése egy aszinkron művelet, amely hosszabb időt is igénybe vehet. Az állapot vagy az eredmény ellenőrzéséhez ugyanazzal az URI-val küldheti el a `GET` kérelmet. A válaszüzenet olyan `provisioningState` mezővel rendelkezik, amely visszaadja az `InProgress` értéket, ha a feltöltési művelet továbbra is működik. Ha `provisioningState` értéke `Succeeded` , a feltöltési művelet befejeződött.

#### <a name="request-syntax"></a>Kérelem szintaxisa

Ha egy egyéni megbízható főtanúsítvánnyal szeretné frissíteni az ISE-t, küldje el a következő HTTPS-javítási kérelmet a [Azure Resource Manager URL-címre, amely az Azure-környezettől](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane)függ, például:

| Környezet | Azure Resource Manager URL-cím |
|-------------|----------------------------|
| Azure Global (több-bérlős) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure China 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Kérelem törzsének szintaxisa egyéni főtanúsítványok hozzáadásához

Itt látható a kérelem törzsének szintaxisa, amely leírja a főtanúsítványok hozzáadásakor használandó tulajdonságokat:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Következő lépések

* [Erőforrás hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
