---
title: Privát végpontok használata Azure Batch-fiókokkal
description: Megtudhatja, hogyan csatlakozhat egy Azure Batch-fiókhoz privát végpontok használatával.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920003"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Privát végpontok használata Azure Batch-fiókokkal

Alapértelmezés szerint a [Azure batch fiókok](accounts.md) nyilvános végponttal rendelkeznek, és nyilvánosan elérhetők. A Batch szolgáltatás lehetővé teszi privát batch-fiókok létrehozását, valamint a nyilvános hálózati hozzáférés letiltását.

Az [Azure Private link](../private-link/private-link-overview.md)használatával [privát végponton](../private-link/private-endpoint-overview.md)keresztül csatlakozhat egy Azure batch fiókhoz. A magánhálózati végpont a virtuális hálózaton belüli alhálózat magánhálózati IP-címeinek halmaza. Ezután korlátozhatja a hozzáférést egy Azure Batch fiókhoz magánhálózati IP-címeken keresztül.

A privát hivatkozás lehetővé teszi, hogy a felhasználók a virtuális hálózatról vagy bármely társ virtuális hálózatról hozzáférjenek Azure Batch fiókhoz. A privát kapcsolatra leképezett erőforrások a VPN-en vagy az [Azure-ExpressRoute](../expressroute/expressroute-introduction.md)keresztül is elérhetők a helyszínen keresztül. Az [automatikus vagy manuális jóváhagyási módszer](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)használatával csatlakozhat egy privát kapcsolattal konfigurált Azure batch-fiókhoz.

> [!IMPORTANT]
> A Azure Batch-beli magánhálózati kapcsolat támogatása jelenleg minden régióban elérhető, kivéve Németország középső régiója, Kelet-Németország, Kelet-Kína, Kelet-Kína 2, Észak-Kína és Észak-Kína 2.

Ez a cikk azt ismerteti, hogyan hozható létre egy privát batch-fiók, és hogyan férhet hozzá egy privát végponton keresztül.

## <a name="azure-portal"></a>Azure Portal

A következő lépésekkel hozzon létre egy privát batch-fiókot a Azure Portal használatával:

1. Az **erőforrás létrehozása** panelen válassza a **Batch szolgáltatás** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
2. Adja meg az előfizetés, az erőforráscsoport, a régió és a Batch-fiók nevét az **alapok** lapon, majd válassza a **Tovább: speciális** lehetőséget.
3. A **speciális** lapon állítsa be a **nyilvános hálózati hozzáférés** **Letiltva** lehetőséget.
4. A **Beállítások** területen válassza a **privát végponti kapcsolatok** , majd a **+ privát végpont** elemet.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Magánhálózati végpontok kapcsolatai":::
5. Az **alapvető beállítások** panelen adja meg vagy válassza ki az előfizetést, az erőforráscsoportot, a magánhálózati végpont erőforrásának nevét és a régió részleteit, majd válassza a **Tovább: erőforrás** elemet.
6. Az **erőforrás** ablaktáblán állítsa be az **erőforrás típusát** **Microsoft.BatCH/batchAccounts** értékre. Válassza ki az elérni kívánt privát batch-fiókot, majd válassza a **Tovább: konfigurálás** lehetőséget.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Privát végpont – erőforrás ablaktábla létrehozása":::
7. A **konfiguráció** ablaktáblán adja meg vagy válassza ki az alábbi adatokat:
   - **Virtuális hálózat**: válassza ki a virtuális hálózatot.
   - **Alhálózat**: válassza ki az alhálózatot.
   - **Integráció a saját DNS-zónával**: válassza az **Igen** lehetőséget. A magánhálózati végponthoz való kapcsolódáshoz DNS-rekordra van szükség. Javasoljuk, hogy a privát végpontot egy privát DNS-zónával integrálja. Saját DNS-kiszolgálókat is használhat, vagy létrehozhat DNS-rekordokat a virtuális gépeken található gazdagép-fájlok használatával.
   - **Saját DNS zóna**: válassza a privatelink \<region\> lehetőséget. batch.azure.com. A magánhálózati DNS-zóna automatikusan meg van határozva. A Azure Portal használatával nem módosítható.
8. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd várjon, amíg az Azure érvényesíti a konfigurációt.
9. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

A magánhálózati végpont kiépítés után a Batch-fiókot a privát végpont használatával is elérheti ugyanazon a virtuális hálózaton lévő virtuális gépekről.

> [!IMPORTANT]
> Az Azure Portalon egy "AuthorizationFailure" üzenet jelenik meg, amely azon a virtuális hálózaton kívül végez műveleteket, amelyen a privát végpont kiépítve lesz.

A Azure Portal IP-címének megtekintése:

1. Válassza a **Minden erőforrás** elemet.
2. Keresse meg a korábban létrehozott privát végpontot.
3. A DNS-beállítások és az IP-címek megtekintéséhez válassza az **Áttekintés** lapot.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Magánhálózati végpont DNS-beállításai és IP-címei":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Ha [Azure Resource Manager sablonnal hoz létre batch-fiókot](quick-create-template.md), módosítsa a sablont úgy, hogy az alább látható módon **PublicNetworkAccess** a **Letiltva** értékre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>DNS-zónák konfigurálása

Használjon [privát DNS-zónát](../dns/private-dns-privatednszone.md) azon az alhálózaton belül, ahol létrehozta a privát végpontot. Konfigurálja úgy a végpontokat, hogy minden magánhálózati IP-cím le legyen képezve egy DNS-bejegyzésre.

A privát végpont létrehozásakor az Azure-ban egyesítheti azt egy [privát DNS-zónával](../dns/private-dns-privatednszone.md) . Ha ehelyett [Egyéni tartományt](../dns/dns-custom-domain.md)használ, úgy kell beállítania, hogy DNS-rekordokat adjon hozzá a privát végpont számára fenntartott MAGÁNHÁLÓZATI IP-címekhez.

## <a name="pricing"></a>Díjszabás

A privát végpontokkal kapcsolatos költségekről az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link/)című témakörben olvashat bővebben.

## <a name="current-limitations-and-best-practices"></a>Jelenlegi korlátozások és ajánlott eljárások

A privát batch-fiók létrehozásakor vegye figyelembe a következőket:

- A privát végponti erőforrásokat a Batch-fiókkal megegyező előfizetésben kell létrehozni.
- A magánhálózati kapcsolatok törléséhez törölnie kell a magánhálózati végpont erőforrását.
- Ha a Batch-fiók nyilvános hálózati hozzáféréssel lett létrehozva, csak privát hozzáférésre lehet módosítani.
- A magánhálózati DNS-zónában lévő DNS-rekordokat a rendszer nem távolítja el automatikusan, amikor töröl egy privát végpontot, vagy ha eltávolít egy régiót a Batch-fiókból. A DNS-rekordokat manuálisan kell eltávolítania, mielőtt hozzáad egy, a magánhálózati DNS-zónához társított új privát végpontot. Ha nem törli a DNS-rekordokat, előfordulhat, hogy a rendszer váratlan adatsíkokat észlel, például a privát végpont eltávolítása vagy a régió eltávolítása után hozzáadott régiók számára.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre batch-készleteket virtuális hálózatokban](batch-virtual-network.md).
- Útmutató a [Batch-készletek nyilvános IP-címek nélküli létrehozásához](batch-pool-no-public-ip-address.md)
- Megtudhatja, hogyan [hozhat létre batch-készleteket a megadott nyilvános IP-címekkel](create-pool-public-ip.md).
- További információ az [Azure Private linkről](../private-link/private-link-overview.md).
