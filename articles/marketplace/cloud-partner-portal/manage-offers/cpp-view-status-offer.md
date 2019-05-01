---
title: Marketplace-ajánlat állapotának megtekintése |} Az Azure Marketplace-en
description: Az Azure és az AppSource-piactér használatával a Cloud Partner portálra szóló ajánlatok állapotának megtekintése
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b8113552edf9dd2f886b5752b2ebc69afc0fda08
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942385"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Az Azure Marketplace és AppSource ajánlatokat közzétételi állapotának megtekintése

Miután létrehozott egy ajánlatra, és különösen a közzétételi folyamat során, az ajánlat állapotát megtekintheti a Cloud Partner portálra.  A teljes közzétételi állapot érhető el a [ **összes kínál** ](../portal-tour/cpp-all-offers-page.md) és [ **jóváhagyások** ](../portal-tour/cpp-approvals-page.md) a portál oldalain.  Az alábbi Állapotjelzők egyik ajánlatok üzenet.  

|            status              |   Leírás                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Az ajánlat létre lett hozva, de a közzétételi folyamat még nem kezdődött meg.            |
| **A közzététel folyamatban**        | Az ajánlat a közzétételi folyamat lépéseit korongot működik.   |
| **Sikertelen közzététel**             | Egy kritikus problémát a Microsoft által felderített érvényesítési vagy felülvizsgálat alatt. |
| **Közzététele megszakítva**           | A közzétevő megszakította az ajánlat közzétételi folyamat.  Ebben az állapotban nem jegyzékből egy meglévő ajánlatot a piactéren. | 
| **Várakozás a publisher jelentkezzen ki** | Az ajánlat Microsoft felülvizsgálták, és most várja a végső ellenőrzés a közzétevő. |
| **Delisted**                   | A korábban közzétett ajánlatot a piactéren el lett távolítva.      | 
|  |  |


## <a name="publishing-status-details"></a>Közzétételi állapot részletei 

Részletesebb információkat tartalmaznak az ajánlat állapota a közzétételi folyamat előrehaladtával megtalálható a **állapot** lapján a **új ajánlat** lapot.  Ezen a lapon találhatók a közzétételi lépéseket az adott ajánlat típusát.  *Vegye figyelembe, hogy száma és különleges lépések gyakran eltérő típusú között.*  Ezen a lapon is azt jelzi, hogy bármely szálankénti függőben lévő problémák váltotta ki a Microsoft ellenőrzési és felülvizsgálati lépéseket, amelyek gyakran igényelnek a közzétevő művelet, a közzétételi folyamat folytatásához.  Ha például az alábbi képen a **állapot** fülre egy új virtuálisgép-ajánlat. 

![A Virtuálisgép-ajánlat állapota lap](./media/vm-offer-pub-steps1.png)

A következő példában **állapot** a tanácsadási szolgáltatást, és a jelentett hiba megjelenítése az érdeklődő beállítások lapján.  Mivel a szükséges tanácsadási szolgáltatások lead felügyelet, ez a hiba ki kell javítani, közzététel folytatása előtt.

![A tanácsadási szolgáltatást bemutató hiba állapota lap](./media/consulting-service-error.png)

Az Azure-alkalmazások utolsó példában állapotánál egy kritikus fontosságú Microsoft tekintse át a probléma.  Gyakori elérésű, tekintse át a problémával kapcsolatos részletes információkat tartalmaz a VSTS-elemre mutató hivatkozást tartalmaz.  További információkért lásd: [közzététele az Azure application ajánlat](cpp-publish-offer.md).

![Tekintse át a probléma megjelenítő Azure-alkalmazáshoz lapja](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>További lépések

Javítsa ki a szálankénti függőben lévő problémák vagy az ajánlat beállításainak frissítése, kell [frissíteni az ajánlatot](./cpp-update-offer.md). 
