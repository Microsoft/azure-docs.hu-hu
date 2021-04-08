---
title: Azure-beli virtuális hálózati szolgáltatásvégpontokra vonatkozó szabályzatok | Microsoft Docs
description: Tudnivalók az Azure-beli szolgáltatási erőforrásokba irányuló virtuális hálózati forgalom szolgáltatásvégpont-szabályzatokkal történő szűréséről
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 633d0b9e454fe2906d98624ac2cdbcc9a8b6c7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022262"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Virtuális hálózati szolgáltatásvégpont-szabályzatok létrehozása az Azure Storage-hez

A Virtual Network (VNet) szolgáltatás-végponti házirendek lehetővé teszik a kimenő virtuális hálózati forgalom szűrését az Azure Storage-fiókokon keresztül a szolgáltatási végponton keresztül, és lehetővé teszi, hogy az adatok csak bizonyos Azure Storage-fiókokra kiszűrése. A végponti szabályzatok részletes hozzáférés-vezérlést biztosítanak az Azure Storage-ba irányuló virtuális hálózati forgalomhoz, amikor a szolgáltatás-végpontot

![A virtuális hálózat kimenő forgalmának védelme az Azure Storage-fiókokkal](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Ez a funkció általánosan elérhető az __Azure Storage__ -hoz az __összes globális Azure-régióban__.

## <a name="key-benefits"></a>Főbb előnyök

A virtuális hálózati szolgáltatásvégpont-szabályzatok a következő előnyöket biztosítják:

- __Nagyobb biztonság az Azure Storage-ba irányuló Virtual Network-forgalom számára__

  [A hálózati biztonsági csoportok Azure-szolgáltatási címkéi](./network-security-groups-overview.md) lehetővé teszik a virtuális hálózat kimenő forgalmának korlátozását adott Azure Storage-régiókra. Ez azonban lehetővé teszi, hogy a forgalmat a kiválasztott Azure Storage-régióban lévő fiókokra irányítsa.
  
  A végponti házirendek segítségével megadhatja azokat az Azure Storage-fiókokat, amelyek engedélyezik a virtuális hálózat kimenő elérését, és korlátozza a hozzáférést az összes többi Storage-fiókhoz. Ez sokkal részletesebb biztonsági szabályozást biztosít a virtuális hálózatról származó adatok kiszűrése védelméhez.

- __Méretezhető, magas rendelkezésre állású szabályzatok az Azure-szolgáltatások adatforgalmának szűréséhez__

   A végpontszabályzatok horizontálisan skálázható, magas rendelkezésre állású megoldásokat biztosítanak a virtuális hálózatokból a szolgáltatásvégpontokon keresztül az Azure-szolgáltatásokba érkező forgalom szűrésére. Nincs szükség további erőforrásokra, hogy központi hálózati berendezéseket kelljen fenntartani a virtuális hálózat ezen forgalmához.

## <a name="json-object-for-service-endpoint-policies"></a>JSON-objektum a szolgáltatási végpont házirendjeihez
Vessünk egy gyors pillantást a szolgáltatás-végponti házirend objektumra.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfiguráció

-   A végpont-házirendeket beállíthatja úgy, hogy az adott Azure Storage-fiókokra korlátozza a virtuális hálózati forgalmat.
-   A végpontszabályzatot a virtuális hálózat egy alhálózatán kell konfigurálni. Az Azure Storage szolgáltatási végpontait engedélyezni kell az alhálózaton a szabályzat alkalmazásához.
-   A végponti házirend lehetővé teszi adott Azure Storage-fiókok hozzáadását a listához a resourceID formátum használatával. Korlátozhatja a hozzáférést
    - az előfizetésben lévő összes Storage-fiók<br>
      `E.g. /subscriptions/subscriptionId`

    - egy erőforráscsoport összes Storage-fiókja<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - egy egyedi Storage-fiók a megfelelő Azure Resource Manager resourceId listázásával. Ez lefedi a blobokba, táblákba, üzenetsorokba, fájlokba és az Azure Data Lake Storage Gen2-be irányuló forgalmat. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Alapértelmezés szerint, ha egyetlen házirend sincs csatlakoztatva végpontokkal rendelkező alhálózathoz, akkor a szolgáltatásban lévő összes Storage-fiókhoz hozzáférhet. Amint konfigurált egy szabályzatot az alhálózaton, kizárólag a szabályzatban meghatározott erőforrásokhoz lehet hozzáférni az adott alhálózat számítási példányairól. A rendszer megtagadja az összes többi Storage-fiók elérését.
-   Ha szolgáltatási végponti házirendeket alkalmaz egy alhálózaton, az Azure Storage *szolgáltatás végponti hatóköre* a regionálisról a **globálisra** lesz frissítve. Ez azt jelenti, hogy az Azure Storage-ba irányuló összes forgalmat a szolgáltatás végpontja biztosítja. A szolgáltatás-végponti szabályzatok globálisan is alkalmazhatók, így a nem kifejezetten engedélyezett Storage-fiókok esetében a rendszer letiltja a hozzáférést. 
-   Egy alhálózaton több szabályzatot is életbe léptethet. Ha több házirend van társítva az alhálózathoz, akkor az ezen házirendek bármelyikében megadott erőforrásokhoz tartozó virtuális hálózati forgalom engedélyezve lesz. A szabályzatokban nem meghatározott szolgáltatási erőforrásokhoz való hozzáférést a rendszer megtagadja.

    > [!NOTE]  
    > A szolgáltatás-végponti házirendek **lehetővé teszik a házirendek használatát**, így a megadott erőforrásokon kívül minden más erőforrás korlátozott. Győződjön meg arról, hogy az alkalmazásokhoz tartozó összes szolgáltatási erőforrás-függőség azonosítható és szerepel a szabályzatban.

- Csak az Azure Resource-modellt használó tárfiókokat lehet megadni a végpontszabályzatban. A klasszikus Azure Storage-fiókok nem támogatják az Azure szolgáltatás-végponti házirendjeit.
- Az RA-GRS másodlagos hozzáférést automatikusan engedélyezi a rendszer, ha az elsődleges fiók szerepel a listán.
- A tárfiókok a virtuális hálózattal megegyező vagy attól eltérő előfizetéshez vagy Azure Active Directory-bérlőhöz is tartozhatnak.

## <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: A társviszonyban álló virtuális hálózatok forgalmának szűréséhez a végpontszabályzatokat egyenként kell érvényesíteni az egyes virtuális hálózatokon.
- Az **internetes forgalom szűrése hálózati berendezésekkel vagy Azure Firewallekkel**: az Azure-szolgáltatások forgalmának szűrése házirendekkel, a szolgáltatási végpontokon keresztül, valamint az internetes vagy az Azure-adatforgalom szűrése készülékeken vagy Azure Firewall keresztül.
- A **virtuális hálózatokon üzembe helyezett Azure-szolgáltatások forgalmának szűrése**: jelenleg az Azure-szolgáltatás végponti házirendjei nem támogatottak a virtuális hálózaton üzembe helyezett felügyelt Azure-szolgáltatásokban. 
- **A helyszínről az Azure-szolgáltatásokba irányuló forgalom szűrése**: A szolgáltatásvégpont-szabályzatok csak a szabályzatokhoz társított alhálózatokról érkező forgalomra vonatkoznak. Az egyes Azure-beli szolgáltatási erőforrásokhoz való helyszíni hozzáférés engedélyezéséhez a forgalmat virtuális hálózati berendezésekkel vagy tűzfalakkal szűrheti.

## <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás
Szolgáltatásvégpont-szabályzatokhoz a központi naplózás nem érhető el. A szolgáltatások erőforrás-naplóival kapcsolatban lásd: [szolgáltatás-végpontok naplózása](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Hibaelhárítási forgatókönyvek
- Hozzáférés megtagadva az előzetes verzióban működő Storage-fiókokban (nem a Geo-párosítási régióban)
  - Az Azure Storage a globális szolgáltatási címkék használatára való frissítésével a szolgáltatási végpontok és a szolgáltatás-végponti házirendek hatóköre már globális. Így az Azure Storage-ba irányuló adatforgalom titkosítva van a szolgáltatási végpontokon, és csak a szabályzatban kifejezetten felsorolt tárolási fiókok férhetnek hozzá.
  - A hozzáférés visszaállításához explicit módon engedélyezze az összes szükséges Storage-fiók listáját.  
  - Forduljon az Azure ügyfélszolgálatához.
- A végpontszabályzatokban szereplő fiókokhoz való hozzáférés meg van tagadva
  - A hálózati biztonsági csoportok vagy a tűzfalak használatával történő szűrés blokkolhatja a hozzáférést
  - Ha a szabályzat eltávolítása/újbóli életbe léptetése miatt megszűnik a kapcsolat:
    - Ellenőrizze, hogy az Azure-szolgáltatás úgy van-e konfigurálva, hogy engedélyezze a virtuális hálózat végpontokon keresztüli elérését, vagy hogy az erőforrás alapértelmezett házirendje az *összes engedélyezése* értékre van állítva.
    - Ellenőrizze, hogy a szolgáltatásdiagnosztika megjeleníti-e a végpontokon átmenő forgalmat.
    - Ellenőrizze, hogy a hálózati biztonsági csoport forgalmának naplói és a tárolási naplók a várakozásoknak megfelelően a végpontokon keresztül történő hozzáférést mutatják-e.
    - Forduljon az Azure ügyfélszolgálatához.
- A szolgáltatásvégpont-szabályzatokban nem szereplő fiókok hozzáférése meg van tagadva
  - Ellenőrizze, hogy az Azure Storage konfigurálva van-e a virtuális hálózat végpontokon keresztüli elérésének engedélyezésére, vagy hogy az erőforrás alapértelmezett házirendje az *összes engedélyezése* értékre van-e állítva.
  - Győződjön meg arról, hogy a fiókok nem **klasszikus tárolási fiókok** az alhálózaton szolgáltatási végponti házirendekkel.
- Egy felügyelt Azure-szolgáltatás leállt a szolgáltatás-végponti házirendnek az alhálózaton való alkalmazása után.
  - A felügyelt szolgáltatások jelenleg nem támogatottak a szolgáltatás-végponti házirendekkel. *Tekintse meg ezt a helyet a frissítésekhez*.
- A felügyelt Storage-fiókokhoz való hozzáférés a szolgáltatás-végponti házirend alhálózaton való alkalmazása után leáll.
  - A felügyelt Storage-fiókok nem támogatottak a szolgáltatás-végponti házirendekkel. Ha be van állítva, a házirendek alapértelmezés szerint megtagadják a hozzáférést az összes felügyelt Storage-fiókhoz. Ha az alkalmazásnak hozzá kell férnie a felügyelt Storage-fiókokhoz, a végpont-házirendeket nem szabad használni ehhez a forgalomhoz.

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpont-szabályzatokat az alhálózatokon egy olyan felhasználó konfigurálhatja, akinek írási hozzáférése van egy virtuális hálózathoz. További információkat is megtudhat az Azure [beépített szerepköreiről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és az egyes engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.

A virtuális hálózatok és az Azure Storage-fiókok lehetnek azonos vagy eltérő előfizetésekben, vagy Azure Active Directory bérlők.

## <a name="limitations"></a>Korlátozások

- Szolgáltatásvégpont-szabályzatokat csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon helyezhet üzembe.
- A virtuális hálózatoknak és a szolgáltatásvégpont-szabályzatnak ugyanabban a régióban kell lenniük.
- Csak akkor alkalmazhat szolgáltatásvégpont-szabályzatokat egy alhálózaton, ha a szolgáltatásvégpontok a szabályzatban szereplő Azure-szolgáltatásokhoz vannak konfigurálva.
- A saját helyszíni hálózatáról az Azure-szolgáltatásokba irányuló forgalomhoz nem használhat szolgáltatásvégpont-szabályzatokat.
- Az Azure által felügyelt szolgáltatások jelenleg nem támogatják a végponti házirendeket. Ez magában foglalja a megosztott alhálózatokban üzembe helyezett felügyelt szolgáltatásokat (például *Azure batch, Azure-hozzáadások, azure Application Gateway, azure VPN Gateway, Azure Firewall*) vagy a dedikált alhálózatok (például *Azure app Service Environment, Azure Redis Cache, Azure API Management, Azure SQL mi, klasszikus felügyelt szolgáltatások*).

 > [!WARNING]
 > A virtuális hálózatokban üzembe helyezett Azure-szolgáltatások, mint például az Azure HDInsight, más Azure-szolgáltatásokhoz, például az Azure Storage-hoz férnek hozzá, hogy lekérjék az infrastruktúrával kapcsolatos követelményeket. A végpontszabályzatok bizonyos erőforrásokra történő korlátozása meggátolhatja, hogy a virtuális hálózatában üzembe helyezett Azure-szolgáltatások hozzáférjenek ezekhez az infrastruktúra-erőforrásokhoz.

- A klasszikus Storage-fiókok nem támogatottak a végponti házirendekben. Alapértelmezés szerint a szabályzatok minden klasszikus tárfiókhoz megtagadják a hozzáférést. Ha az alkalmazásának hozzá kell férnie az Azure Resource Managerhez és a klasszikus tárfiókokhoz, ne használjon végpontszabályzatokat a köztük zajló forgalom vezérléséhez.

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpont-szabályzatok használata nem jár további költségekkel. A szolgáltatásvégpontok esetében az Azure-szolgáltatások (például az Azure Storage) aktuális díjszabási modellje az irányadó.

A következő korlátozásokat érvényesíti a rendszer a szolgáltatásvégpont-szabályokon: 

 |Erőforrás | Alapértelmezett korlát |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Következő lépések

- Tudnivalók [a virtuális hálózati szolgáltatásvégpont-szabályzatok konfigurálásáról](virtual-network-service-endpoint-policies-portal.md)
- További információk a [virtuális hálózati szolgáltatásvégpontokról](virtual-network-service-endpoints-overview.md)
