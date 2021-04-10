---
title: Közzétételi útmutató ajánlat típusa szerint – Microsoft kereskedelmi piactér
description: Ez a cikk a Microsoft kereskedelmi piactéren elérhető ajánlati típusokat ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 04/06/2021
ms.openlocfilehash: 381ee45bd678ef22222483675ddf001e16970628
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552053"
---
# <a name="publishing-guide-by-offer-type"></a>Közzétételi útmutató ajánlattípusok szerint

Ez a cikk a kereskedelmi piactéren elérhető ajánlati típusokat ismerteti. Az *ajánlat típusa* határozza meg az ajánlat struktúráját, amely tartalmazza a kereskedelmi piactéren bemutatott metaadatokat, összetevőket és egyéb tartalmakat.

A [közzétételi lehetőség](determine-your-listing-type.md)kiválasztása után ki kell választania az ajánlat típusát, mielőtt megkezdené az ajánlat létrehozását a partner Centerben. Az ajánlat típusa megegyezik a közzétenni kívánt megoldás, alkalmazás vagy szolgáltatási ajánlat típusával, valamint a Microsoft-termékekkel és-szolgáltatásokkal való összehangolásával.

> [!NOTE]
> Az ajánlat típusának kiválasztása után az ajánlat nem módosítható másik típusra. Egy másik ajánlat típusának létrehozásához létre kell hoznia egy új ajánlatot.

A különböző közzétételi lehetőségek, listaelemek, kiépítés vagy díjszabás lehetővé tételéhez többféle módon is konfigurálhat egyetlen ajánlatot. A közzétételi lehetőség és az ajánlat típusának konfigurációja is igazodik az ajánlat támogathatóságához és a technikai követelményekhez.

Ügyeljen arra, hogy az ajánlat létrehozása előtt tekintse át az online áruház és az ajánlat típusra vonatkozó támogathatósági követelményeit, valamint a technikai közzétételi követelményeket.

## <a name="list-of-offer-types"></a>Ajánlati típusok listája

A következő táblázat a partner Center kereskedelmi Piactéri ajánlatának típusait mutatja be.

| **Ajánlat típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| [**Azure-alkalmazás**](plan-azure-application-offer.md) | Kétféle Azure-alkalmazási csomag létezik: _megoldás sablon_ és _felügyelt alkalmazás_. Mindkét díjcsomag támogatja a megoldás üzembe helyezésének és konfigurálásának automatizálását egyetlen virtuális gépen (VM) túl. Automatizálhatja több erőforrás, többek között a virtuális gépek, a Hálózatkezelés és a tárolási erőforrások megadásának folyamatát összetett megoldások, például IaaS-megoldások biztosítása érdekében. Mindkét projekttípus számos különböző Azure-erőforrást alkalmazhat, beleértve a virtuális gépeket, de nem korlátozódik rájuk.<ul><li>A **megoldási sablonok** az egyik fő módszer a megoldás közzétételére a kereskedelmi piactéren. A megoldási sablonok csomagjai nem vonhatók le a kereskedelmi piactéren, de használhatók a kereskedelmi Piactéren keresztül számlázott fizetős virtuális gépek üzembe helyezésére. Használja a megoldás sablonjának típusát, ha az ügyfél felügyeli a megoldást, és a tranzakciók számlázása egy másik csomagon keresztül történik.</li><br><li>A **felügyelt alkalmazási** csomagok segítségével egyszerűen hozhat létre és biztosíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfelei számára. Ugyanazokkal a képességekkel rendelkeznek, mint a megoldási sablonok, és néhány fő különbség:</li><ul><li> Az erőforrások üzembe helyezése egy erőforráscsoporthoz történik, és az alkalmazás közzétevője felügyeli. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz.</li><li>Közzétevőként meg kell adnia a megoldás folyamatos támogatásának költségeit, és a tranzakciók a kereskedelmi Piactéren keresztül támogatottak.</li></ul>Használja a felügyelt alkalmazáscsomag típusát, ha Ön vagy az ügyfél megköveteli, hogy a megoldást egy partner felügyelje, vagy egy előfizetésen alapuló megoldást helyezzen üzembe.</ul> |
| [**Azure-tároló**](marketplace-containers.md) | Használja az Azure Container ajánlat típusát, ha a megoldás egy Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép. |
| [**Azure-beli virtuális gép**](marketplace-virtual-machines.md) | Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát. |
| [**Tanácsadási szolgáltatás**](./plan-consulting-service-offer.md) | A tanácsadási szolgáltatások segítségével az ügyfeleket összekapcsolhatja a szolgáltatásokkal az Azure, a Dynamics 365 vagy a Power Suite szolgáltatások használatának támogatásához és bővítéséhez.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Közzéteheti a Dynamics 365 Business Central, a Dynamics 365 Customer engagement, a Power apps és a Finance és az Operations alkalmazások szolgáltatásait felépítő AppSource-ajánlatokat.|
| [**IoT Edge modul**](iot-edge-module.md) | Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. |
| [**Felügyelt szolgáltatás**](./plan-managed-service-offer.md) | Az [Azure Lighthouse](../lighthouse/overview.md)használatával felügyelt szolgáltatásokat hozhat létre, és kezelheti az ügyfelek által delegált előfizetéseket vagy erőforráscsoportokat.|
| [**Power bi alkalmazás** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Közzéteheti azokat a AppSource-ajánlatokat, amelyek Power BI és Microsoft 365 kiépítésére vagy kiterjesztésére épülnek.|
| [**Szolgáltatásként szolgáló szoftver**](plan-saas-offer.md) | A szolgáltatott szoftver (SaaS) ajánlat típusa lehetővé teszi az ügyfél számára, hogy előfizetésként vásárolja meg SaaS-alapú, technikai megoldásait. Az SaaS-ajánlatok egyszeri bejelentkezési követelményeivel kapcsolatos információkért lásd: [Az Azure ad és a transactd SaaS-ajánlatok a kereskedelmi piactéren](azure-ad-saas.md). |


## <a name="next-steps"></a>Következő lépések

- Az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez tekintse át a megfelelő cikkben szereplő támogathatósági követelményeket.
- Tekintse át az egyes online áruházak közzétételi mintáit, példákat arra, hogy a megoldás hogyan mutasson az ajánlat típusára és a konfigurációra.