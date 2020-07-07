---
title: Ajánlott eljárások a hosszú ideig futó műveletekhez Azure Analysis Servicesban | Microsoft Docs
description: Ez a cikk a hosszú ideig futó műveletek ajánlott eljárásait ismerteti.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81428107"
---
# <a name="best-practices-for-long-running-operations"></a>Ajánlott eljárások a hosszú ideig futó műveletekhez

Azure Analysis Services a *csomópont* egy olyan gazdagép virtuális gépet jelöl, amelyben a kiszolgálói erőforrás fut. Bizonyos műveletek, például a hosszan futó lekérdezések, a frissítési műveletek és a lekérdezési Felskálázási szinkronizálás meghiúsulhat, ha egy kiszolgálói erőforrás egy másik csomópontra kerül át. A forgatókönyv gyakori hibaüzenetei a következők:

- "Hiba történt egy hosszú ideig futó XMLA-kérelem keresése közben. Előfordulhat, hogy a kérést a szolgáltatás frissítése vagy a kiszolgáló újraindítása megszakította. "
- A (z) "" AZONOSÍTÓJÚ feladat a <guid> (z) "" modellhez tartozó <database> szolgáltatási hiba miatt megszakadt, a következő üzenettel: "a frissítési kérelem megszakítása, mivel a frissítés nélkül elakadt. Ez egy belső szolgáltatási probléma. Ha a probléma többször is előfordul, küldje el újra a feladatot, vagy küldjön egy jegyet a segítségre. "

Számos oka lehet annak, hogy a hosszan futó műveletek megszakadnak. Például az Azure-ban, például a következő frissítésekkel: 
- Operációs rendszer javításai 
- Biztonsági frissítések
- Azure Analysis Services szolgáltatás frissítései
- Service Fabric frissítések. A Service Fabric egy több Microsoft Cloud Services által használt platform-összetevő, beleértve a Azure Analysis Services is.

A szolgáltatásban megjelenő frissítések mellett a terheléselosztás miatt a szolgáltatások csomópontok közötti természetes mozgása is fennáll. A csomópontok áthelyezése a Cloud Service várt részét képezi. Azure Analysis Services megpróbálja csökkenteni a csomópontok mozgásának hatásait, de nem lehet teljesen kiküszöbölni őket. 

A csomópontok forgalmán kívül más hibák is előfordulhatnak. Előfordulhat például, hogy az adatforrás adatbázis-rendszere offline állapotban van, vagy a hálózati kapcsolat megszakad. Ha a frissítés során a partícióhoz 10 000 000 sor tartozik, és a 9 milliomodik során hiba történik, a rendszer nem indítja újra a frissítést a meghibásodási ponton. A szolgáltatásnak újra kell kezdődnie az elejétől. 

## <a name="refresh-rest-api"></a>REST API frissítése

A szolgáltatás megszakításai nagy kihívást jelenthetnek a hosszú ideig futó műveletek, például az Adatfrissítés esetén. Azure Analysis Services tartalmaz egy REST API, amely segít csökkenteni a szolgáltatások megszakításának negatív hatásait. További információ: [aszinkron frissítés a REST API](analysis-services-async-refresh.md).
 
A REST APIon kívül más módszerek is használhatók a lehetséges problémák minimalizálására a hosszú ideig futó frissítési műveletek során. A fő cél az, hogy ne kelljen újraindítani a frissítési műveletet az elejétől, hanem a kisebb kötegekben végezheti el a frissítését, amelyeket a fázisokban lehet véglegesíteni. 
 
A REST API lehetővé teszi az ilyen újraindítást, de nem teszi lehetővé a partíciók létrehozásának és törlésének teljes rugalmasságát. Ha egy forgatókönyvhöz összetett adatkezelési műveletekre van szükség, a megoldásnak tartalmaznia kell a batchbe való beágyazás valamilyen formáját a logikájában. Ha például tranzakciókat használ az adatok több, különálló kötegekben történő feldolgozására, akkor a nem igényelheti az újraindítást az elejétől, hanem egy köztes ellenőrzőponttól. 
 
## <a name="scale-out-query-replicas"></a>Felskálázási lekérdezés replikái

Akár REST-, akár egyéni logikát használ, az ügyfélalkalmazások lekérdezései továbbra is inkonzisztens vagy közbenső eredményeket adhatnak vissza a kötegek feldolgozásakor. Ha a feldolgozás során az ügyfélalkalmazás által visszaadott konzisztens adatmennyiségre van szükség, és a modell adatainak közbenső [állapotban vannak, a](analysis-services-scale-out.md) kibővített lekérdezési replikákat használhatja.

Ha csak olvasható lekérdezési replikákat használ, míg a frissítések kötegekben vannak elvégezve, az ügyfélalkalmazás felhasználói továbbra is lekérhetik az adatok régi pillanatképét az írásvédett replikák esetében. A frissítések befejeződése után egy szinkronizálási művelet végrehajtásával a csak olvasható replikák naprakészen helyezhetők.


## <a name="next-steps"></a>További lépések

[Aszinkron frissítés a REST API-val](analysis-services-async-refresh.md)  
[Az Azure Analysis Services horizontális felskálázása](analysis-services-scale-out.md)  
[Analysis Services magas rendelkezésre állás](analysis-services-bcdr.md)  
[Újrapróbálkozási útmutató az Azure-szolgáltatásokhoz](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

