---
title: Azure Private Link az Azure Data Factoryhez
description: Ismerje meg, hogyan működik az Azure Private link a Azure Data Factoryban.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 9e4d686f582a202dbc543620c7bf73dc4e7adb22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389178"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link az Azure Data Factoryhez

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Az Azure Private link használatával az Azure-ban egy privát végponton keresztül kapcsolódhat különböző platformokhoz (az Azure-ban) üzemelő példányokhoz. A privát végpont egy magánhálózati IP-cím egy adott virtuális hálózaton és alhálózaton belül. A privát kapcsolati funkciót támogató, a következő helyen található, a Private link [dokumentációját](../private-link/index.yml)tartalmazó, 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Biztonságos kommunikáció az ügyfél-hálózatok és a Azure Data Factory között 
Beállíthat egy Azure-beli virtuális hálózatot hálózatának felhőbeli logikai megfelelőjeként. Ez a következő előnyökkel jár:
* Azure-erőforrásai védettek lehetnek a nyilvános hálózatokon elkövetett támadásokkal szemben.
* A hálózatok biztonságosan kommunikálhatnak a Data Factoryvel. 

Emellett helyszíni hálózatot csatlakoztathat a virtuális hálózathoz egy IPsec-védelemmel ellátott VPN-kapcsolat (helyek közötti kapcsolat) vagy egy Azure ExpressRoute-kapcsolat (privát társhálózat létesítése) beállításával. 

Telepítheti a saját üzemeltetésű integrációs modult egy helyszíni gépre vagy a virtuális hálózatban lévő virtuális gépre is. Ez az alábbiakat teszi lehetővé:
* Másolási tevékenységeket végezhet a felhőalapú adattárak és a magánhálózaton található adattárak között.
* Átalakítási tevékenységeket küldhet a helyszíni vagy Azure-beli virtuális hálózaton lévő számítási erőforrásokra vonatkozóan. 

A Azure Data Factory és az ügyfél virtuális hálózata között számos kommunikációs csatorna szükséges, ahogy az a következő táblázatban látható:

| Tartomány | Port | Leírás |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory létrehozásához és figyeléséhez szükséges vezérlő síkja. |
| `*.{region}.datafactory.azure.net` | 443 | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. |
| `*.servicebus.windows.net` | 443 | A saját üzemeltetésű integrációs modul szükséges az interaktív létrehozáshoz. |
| `download.microsoft.com` | 443 | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. |

A Azure Data Factory saját hivatkozásának támogatásával a következőket teheti:
* Hozzon létre egy privát végpontot a virtuális hálózaton.
* Engedélyezze a magánhálózati kapcsolatokat egy adott adatelőállító-példánnyal. 

A Azure Data Factory szolgáltatással folytatott kommunikáció a privát kapcsolaton keresztül érhető el, és segít a biztonságos privát kapcsolat biztosításában. 

![Azure Data Factory architektúrához tartozó privát hivatkozás ábrája.](./media/data-factory-private-link/private-link-architecture.png)

A Private link Service minden korábbi kommunikációs csatornán való engedélyezése a következő funkciókat kínálja:
- **Támogatott**:
   - A virtuális hálózatban megadhatja és figyelheti az adatokat előállítót, még akkor is, ha letiltja az összes kimenő kommunikációt.
   - A saját üzemeltetésű integrációs modul és a Azure Data Factory szolgáltatás közötti kommunikáció biztonságos, magánhálózati környezetben is elvégezhető. A saját üzemeltetésű integrációs modul és a Azure Data Factory szolgáltatás közötti forgalom privát kapcsolaton keresztül halad. 
- **Jelenleg nem támogatott**:
   - A saját üzemeltetésű integrációs modult használó interaktív szerzői műveletek, például a kapcsolat tesztelése, a mappák listájának és a táblák listájának lekérése, a séma beolvasása és az előnézeti adatmegjelenítés.
   - Ha engedélyezi az automatikus frissítést, a saját üzemeltetésű integrációs modul új verziója automatikusan letölthető a Microsoft letöltőközpontból.

   > [!NOTE]
   > A jelenleg nem támogatott funkciók esetében továbbra is konfigurálnia kell a korábban említett tartományt és portot a virtuális hálózaton vagy a vállalati tűzfalon. 

   > [!NOTE]
   > A Azure Data Factory privát végponton keresztül történő csatlakozás csak a saját üzemeltetésű integrációs modulra vonatkozik a adat-előállítóban. A szinapszis nem támogatja.

> [!WARNING]
> Társított szolgáltatás létrehozásakor győződjön meg arról, hogy a hitelesítő adatai egy Azure Key vaultban vannak tárolva. Ellenkező esetben a hitelesítő adatok nem fognak működni, ha engedélyezi a Azure Data Factoryban található privát hivatkozást.

## <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai
Privát végpont létrehozásakor a rendszer a Data Factory DNS CNAME erőforrásrekordot a "privatelink" előtaggal rendelkező altartományban lévő aliasra frissíti. Alapértelmezés szerint a "privatelink" altartománynak megfelelő [privát DNS-zónát](../dns/private-dns-overview.md)is létrehozunk, A DNS a saját végpontokhoz tartozó erőforrásrekordokat.

Ha a VNet kívülről oldja fel az adatgyár-végpont URL-címét a privát végponttal, a rendszer feloldja az adatok gyári szolgáltatásának nyilvános végpontját. A magánhálózati végpontot futtató VNet feloldva a tárolási végpont URL-címe feloldja a magánhálózati végpont IP-címét.

A fenti ábrán látható példában a "DataFactoryA" Data Factory DNS-erőforrásrekordok a privát végpontot üzemeltető VNet kívülről oldhatók fel, a következők:

| Név | Típus | Érték |
| ---------- | -------- | --------------- |
| DataFactoryA. {Region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {Region}. privatelink. DataFactory. Azure. net |
| DataFactoryA. {Region}. privatelink. DataFactory. Azure. net | CNAME   | < a adatfeldolgozó szolgáltatás nyilvános végpontja > |
| < a adatfeldolgozó szolgáltatás nyilvános végpontja >  | A | < a adatfeldolgozó szolgáltatás nyilvános IP-címe > |

A DataFactoryA tartozó DNS-erőforrásrekordok a privát végpontot üzemeltető VNet feloldva a következők:

| Név | Típus | Érték |
| ---------- | -------- | --------------- |
| DataFactoryA. {Region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {Region}. privatelink. DataFactory. Azure. net |
| DataFactoryA. {Region}. privatelink. DataFactory. Azure. net   | A | < magánhálózati végpont IP-címe > |

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek képesnek kell lenniük az Data Factory végpont teljes tartománynevének feloldására a magánhálózati végpont IP-címére. A DNS-kiszolgálót úgy kell konfigurálni, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a "DataFactoryA" rekordokat. {Region}. privatelink. DataFactory. Azure. net "a magánhálózati végpont IP-címével.

A saját DNS-kiszolgáló magánhálózati végpontok támogatására való konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Azure virtuális hálózatokon található erőforrások névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Privát hivatkozás beállítása Azure Data Factoryhoz
[A Azure Portal](../private-link/create-private-endpoint-portal.md)használatával saját végpontokat is létrehozhat.

Kiválaszthatja, hogy a saját üzemeltetésű integrációs modult nyilvános végponton vagy privát végponton keresztül Azure Data Factoryhoz kívánja-e kapcsolni. 

![Képernyőkép a saját üzemeltetésű Integration Runtime nyilvános elérésének blokkolásáról.](./media/data-factory-private-link/disable-public-access-shir.png)


Az Azure-beli adatgyárat a Azure Portalban is megtekintheti, és létrehozhat egy privát végpontot, ahogy az itt látható:

![Képernyőkép a "Private Endpoint connections" panelről privát végpont létrehozásához.](./media/data-factory-private-link/create-private-endpoint.png)

Az **erőforrás** lépésében válassza a **Microsoft. DataFactory/gyárak** **Erőforrás típusa** lehetőséget. Ha a saját üzemeltetésű integrációs modul és a Azure Data Factory szolgáltatás közötti kommunikációhoz szeretne privát végpontot létrehozni, válassza a **DataFactory** **célként alerőforrásként** lehetőséget.

![Képernyőkép a "Private Endpoint connections" panelről az erőforrás kiválasztásához.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> A nyilvános hálózati hozzáférés letiltása csak a saját üzemeltetésű integrációs modulra érvényes, nem Azure Integration Runtime és SQL Server Integration Services (SSIS) Integration Runtime.

Ha privát végpontot szeretne létrehozni a virtuális hálózatban található adatfeldolgozó létrehozásához és figyeléséhez, válassza  a portál **célként alerőforrásként** lehetőséget.

> [!NOTE]
> Továbbra is elérheti a Azure Data Factory portált egy nyilvános hálózaton keresztül, miután létrehozta a portálon a privát végpontot.

## <a name="next-steps"></a>Következő lépések

- [Adat-előállító létrehozása az Azure Data Factory felhasználói felületén](quickstart-create-data-factory-portal.md)
- [Az Azure Data Factory bemutatása](introduction.md)
- [Visual authoring in Azure Data Factory](author-visually.md)