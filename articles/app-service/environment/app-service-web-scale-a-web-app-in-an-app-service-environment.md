---
title: Alkalmazás méretezése a bemutató v1-ben
description: Alkalmazás méretezése egy App Service Environmentban. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86220558"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Alkalmazások méretezése egy App Service Environment v1-ben
A Azure App Service általában három dolgot lehet méretezni:

* díjszabási csomag
* feldolgozói méret 
* példányok száma.

Egy szakadásban nem szükséges kiválasztani vagy módosítani a díjszabási tervet.  A képességek tekintetében már a prémium szintű díjszabási szinten is elérhető.  

A feldolgozói méretek tekintetében a beosztotti rendszergazda hozzárendelheti az egyes munkakészletekhez használandó számítási erőforrás méretét.  Ez azt jelenti, hogy az 1. munkavégző készletet P4 számítási erőforrásokkal és 2. munkavégző készlettel rendelkezik, ha szükséges, P1 számítási erőforrásokkal.  Nem kell megadniuk a méret sorrendjét.  A méretek és díjszabásuk részletes ismertetését itt tekintheti meg [Azure app Service díjszabásban][AppServicePricing].  Ezzel a beállítással a Web Apps és a App Service csomagok skálázási beállításai megmaradnak a App Service Environment:

* munkavégző készlet kiválasztása
* példányok száma

Bármelyik tétel módosítása a szolgáltatott App Service-csomagokhoz tartozó megfelelő felhasználói felületen történik.  

![Képernyőkép a méretezési szolgáltatási csomag és a Worker Pool szolgáltatási csomag részleteinek megtekintéséhez.][1]

Az ASP nem méretezhető fel az ASP-ben lévő munkavégző készletben elérhető számítási erőforrások számánál túl.  Ha számítási erőforrásokra van szüksége az adott munkavégző készletben, akkor hozzá kell adnia a beszerzéshez a beküldés-rendszergazdát.  A következő témakörben olvashat bővebben [arról, hogyan konfigurálhatja][HowtoConfigureASE]a betekintő adatokat: app Service környezet konfigurálása.  Érdemes lehet kihasználnia a kisegítő lehetőségekkel, hogy kihasználhassa a kapacitást az ütemterv vagy a mérőszámok alapján.  Ha további részleteket szeretne megtudni arról, hogyan konfigurálhatja az autoskálázást a beadási környezettel kapcsolatban, tekintse meg, [hogyan kell konfigurálni az autoskálázást app Service Environment][ASEAutoscale]

Több app Service-csomagot is létrehozhat különböző munkavégző készletekből származó számítási erőforrásokkal, vagy használhatja ugyanazt a munkavégző készletet.  Ha például (10) elérhető számítási erőforrások találhatók az 1. munkavégző készletben, dönthet úgy, hogy létrehoz egy app Service-csomagot (6) számítási erőforrásokkal, és egy második app Service-csomagot, amely (4) számítási erőforrásokat használ.

### <a name="scaling-the-number-of-instances"></a>A példányok számának méretezése
Amikor először hozza létre a webalkalmazást egy App Service Environment, akkor 1 példánnyal kezdődik.  Ezután további példányokat is kibővítheti, hogy további számítási erőforrásokat biztosítson az alkalmazáshoz.   

Ha a beszerzett eszköz kapacitása elegendő, akkor ez elég egyszerű.  Nyissa meg a App Service tervet, amely a vertikálisan felskálázást és a méretezést választja.  Ekkor megnyílik a felhasználói felület, amelyen manuálisan állíthatja be az ASP méretezési szabályait, vagy konfigurálhatja az ASP-re vonatkozó autoskálázási szabályokat.  Az alkalmazás manuális méretezéséhez egyszerűen állítsa be a ***skálázást** _ értékre *_egy olyan példányszámot, amelyet manuálisan adtam meg_* *.  Innen húzza a csúszkát a kívánt mennyiségre, vagy írja be a csúszkát a csúszka melletti mezőbe.  

![Képernyőfelvétel: Itt állíthatja be, hogy hol állítható be az ASP méretezése, illetve hogyan konfigurálható az ASP-re vonatkozó autoskálázási szabályok konfigurálása.][2] 

Egy olyan ASP-ben lévő ASP-re vonatkozó autoskálázási szabályok ugyanúgy működnek, mint a szokásos módon.  A ***CPU százalékos** arányát a _*_Scale by (méret_*_ ) lehetőségnél kiválaszthatja, és létrehozhat az ASP számára a CPU százalékán alapuló autoskálázási szabályokat, vagy összetettebb szabályokat is létrehozhat a _ *_Schedule és Performance Rules_* * használatával.  Az autoscale konfigurálásával kapcsolatos további részletekért tekintse meg az [alkalmazás méretezése Azure app Serviceban][AppScale]című útmutatót. 

### <a name="worker-pool-selection"></a>Munkavégző készlet kiválasztása
Ahogy azt korábban említettük, a munkavégző készlet kiválasztása az ASP felhasználói felületéről érhető el.  Nyissa meg a méretezni kívánt ASP panelt, és válassza a munkavégző készlet lehetőséget.  Megjelenik az összes munkavégző készlet, amelyet a App Service Environment konfigurált.  Ha csak egy munkavégző készlettel rendelkezik, akkor csak a felsorolt készlet jelenik meg.  Ha módosítani szeretné a munkavégző készletet az ASP-ben, egyszerűen válassza ki azt a munkavégző készletet, amelyre a App Service tervet szeretné áthelyezni.  

![Képernyőkép, amely azt mutatja, hogy hol módosíthatja az ASP munkavégző készletét.][3]

Mielőtt áthelyezi az ASP-t az egyik munkavégző készletből a másikba, fontos, hogy elegendő kapacitással rendelkezzen az ASP-hez.  A munkavégző készletek listáján nem csak a Worker Pool neve szerepel, de azt is láthatja, hogy hány alkalmazott áll rendelkezésre a munkavégző készletben.  Győződjön meg arról, hogy elegendő példány áll rendelkezésre a App Service tervének tárolására.  Ha több számítási erőforrásra van szüksége a munkavégző készletben, amelyet át szeretne helyezni, akkor adja hozzá a betöltési rendszergazdát.  

> [!NOTE]
> Ha egy ASP-t egy munkavégző készletből helyez át, akkor az ASP-ben lévő alkalmazások hidegen indulnak.  Ez a kérelmek lassú futását okozhatja, mivel az alkalmazás az új számítási erőforrásokon hidegen indult el.  A hidegindítás elkerülhető a Azure App Service [alkalmazás bemelegítő funkciójának][AppWarmup] használatával.  A cikkben ismertetett alkalmazás-inicializálási modul is működik, mert az inicializálási folyamatot akkor is meghívja a rendszer, ha az alkalmazások az új számítási erőforrásokon is hidegen kezdődnek. 
> 
> 

## <a name="getting-started"></a>Első lépések
App Service környezetek megismeréséhez tekintse meg a [app Service Environment létrehozása][HowtoCreateASE] című témakört.

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
