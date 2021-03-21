---
title: Erőforrások és kvóták kezelése
titleSuffix: Azure Purview
description: Ismerje meg az Azure-beli hatáskörébe tartozó erőforrásokra vonatkozó kvótákat és korlátozásokat, valamint a kvóta növelésének módját.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938834"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Erőforrások kvótáinak kezelése és növelése az Azure hatáskörébe
 
Az Azure hatáskörébe egy felhőalapú szolgáltatás, amelyet az adatfelhasználók használhatnak. Az Azure hatáskörébe tartozik, hogy központilag kezelje az adatkezelést az adatközpontban, és a Felhőbeli és a helyszíni környezeteket is felhasználja. A szolgáltatás lehetővé teszi az üzleti elemzők számára, hogy értelmes üzleti feltételek alapján keressenek releváns adatokat. Ha a korlátot az előfizetése maximumán szeretné növelni, forduljon az ügyfélszolgálathoz.
 
## <a name="azure-purview-limits"></a>Az Azure Purview korlátai
 
|**Erőforrás**|  **Alapértelmezett korlát**  |**Felső korlát**|
|---|---|---|
|A hatáskörébe tartozó fiókok régiónként, bérlők szerint (az összes előfizetés együtt)|3|Kapcsolatfelvétel a támogatási szolgáltatással|
|Virtuális mag elérhető a vizsgálathoz|160|160|
|Egyidejű vizsgálatok egy adott ponton. A korlát a beolvasott adatforrások típusától függ *|5 | 10 |
|Az a maximális időtartam, ameddig egy vizsgálat futtatható|7 nap|7 nap|
|API-hívások, fiókok száma|10 millió API/hónap 4 kapacitási egység platform mérethez. <br>40M API-k/hónap a 16 kapacitású egységek platformjának méretéhez |10 millió API/hónap 4 kapacitási egység platform mérethez. <br>40M API-k/hónap a 16 kapacitású egységek platformjának méretéhez|
|Tárterület, fiók szerint|10 GB a 4 kapacitású egység platformjának méretéhez. <br>40 GB a 16 kapacitási egység platformjának méretéhez |10 GB a 4 kapacitású egység platformjának méretéhez. <br> 40 GB a 16 kapacitási egység platformjának méretéhez |
|Az eszközök mérete egy fiókban|100M fizikai eszközök |Kapcsolatfelvétel a támogatási szolgáltatással|
|Egy eszköz maximális mérete egy katalógusban|2 MB|2 MB|
|Az eszköz nevének és besorolási nevének maximális hossza|4 KB|4 KB|
|Az eszköz tulajdonságainak és értékének maximális hossza|32 KB|32 KB|
|A besorolási attribútum nevének és értékének maximális hossza|32 KB|32 KB|
|A szószedethez tartozó használati feltételek maximális száma felhasználónként|100K|100K|
 
* A saját üzemeltetésű integrációs modul forgatókönyvei kívül esnek a fenti táblázatban meghatározott korlátok hatókörén. 
 
## <a name="next-steps"></a>Következő lépések
 
> [!div class="nextstepaction"]
>[Oktatóanyag: az Azure hatáskörébe tartozó adatvizsgálat](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Oktatóanyag: navigáljon a kezdőlapon, és keressen rá egy eszközre](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Oktatóanyag: az adatforrások tallózása és a kifejlődésük megtekintése](tutorial-browse-and-view-lineage.md)
