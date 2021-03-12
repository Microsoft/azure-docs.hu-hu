---
title: SaaS-ajánlat tesztelése és közzététele a Microsoft kereskedelmi piactéren
description: A partner Center használatával elküldheti SaaS-ajánlatát az előzetes verzióra, megtekintheti az ajánlatát, tesztelheti és közzéteheti a Microsoft kereskedelmi piactéren.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ea647591194dfd6ba0bf818f4ce4d3a2005f0bc4
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630782"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>SaaS-ajánlat tesztelése és közzététele a kereskedelmi piactéren

Ez a cikk azt ismerteti, hogyan használhatja a partner centert az SaaS-ajánlat közzétételre való küldéséhez, az ajánlat előzetes megtekintéséhez, teszteléshez, majd a kereskedelmi piactéren való közzétételéhez. Már létre kell hoznia egy ajánlatot, amelyet közzé szeretne tenni.

## <a name="submit-your-offer-for-publishing"></a>Ajánlatának elküldése közzétételre

1. Jelentkezzen be a kereskedelmi piactér Irányítópultra a [partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Az **Áttekintés** lapon válassza ki a közzétenni kívánt ajánlatot.
1. A portál jobb felső sarkában válassza a **felülvizsgálat és közzététel** lehetőséget.
2. Győződjön meg arról, hogy az egyes lapok **állapot** oszlopa **befejeződött**. A három lehetséges állapot a következő:

   - **Nincs elindítva** – a lap hiányos.
   - **Hiányos** – a lap nem tartalmaz szükséges információkat, vagy hibákat kell rögzítenie. Vissza kell lépnie az oldalra, és frissítenie kell.
   - **Befejezés** – a lap elkészült. Minden szükséges információ meg lett adta, és nincsenek hibák.

1. Ha bármelyik oldal állapota nem **fejeződött** be, válassza ki a lap nevét, javítsa ki a problémát, mentse a lapot, majd válassza a **felülvizsgálat és közzététel** újra lehetőséget, hogy visszatérjen ehhez az oldalhoz.
1. Az összes oldal befejezése után a **tanúsítványok megjegyzései** mezőben adja meg a minősítési csapat tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve. Az alkalmazás megértéséhez hasznos kiegészítő megjegyzéseket is megadhat.
1. Az ajánlat közzétételi folyamatának elindításához válassza a **Közzététel** lehetőséget. Megjelenik az **ajánlat áttekintése** lap, és megjeleníti az ajánlat **közzétételének állapotát**.

Az ajánlat közzétételének állapota megváltozik, ahogy a közzétételi folyamaton halad át. A folyamat részletes ismertetését lásd: [érvényesítési és közzétételi lépések](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Az ajánlat előnézete és tesztelése

Ha az ajánlat készen áll a kijelentkezésre, küldjön Önnek egy e-mailt, amely kéri, hogy tekintse át és hagyja jóvá az ajánlat előzetes verzióját. Az **ajánlat áttekintés** lapját is frissítheti a böngészőben, és megtudhatja, hogy az ajánlat elérte-e a közzétevő kijelentkezési fázisát. Ha igen, a **Go Live** gomb és az előzetes verziójú hivatkozások is elérhetők lesznek. A Microsoft AppSource előzetes verzióra, az Azure Marketplace előzetes verziójára, illetve az ajánlat létrehozásakor választott beállításokra vonatkozó hivatkozás is elérhető. Ha úgy dönt, hogy az ajánlatot a Microsofton keresztül értékesíti, akkor bárki, aki hozzá lett adva az előzetes verzió célközönségéhez, tesztelheti az ajánlat beszerzését és üzembe helyezését, hogy az megfeleljen a jelen szakaszban foglalt követelményeknek.

Az alábbi képernyőfelvételen egy SaaS-ajánlat **ajánlat-áttekintés** lapja látható, amely két előzetes verzióra mutató hivatkozást tartalmaz a **Go Live** gomb alatt. Az ezen az oldalon látható ellenőrzési lépések az ajánlat létrehozásakor megadott beállításoktól függően változnak.

![A partneri központban elérhető ajánlat áttekintés lapját mutatja be. Megjelenik a Go Live gomb és az előzetes verzió hivatkozásai. Az ellenőrzési jelentés megtekintése hivatkozás az automatikus ellenőrzés alatt is látható.](./media/review-publish-offer/publish-status-saas.png)

A következő lépésekkel tekintheti meg az ajánlatát.

1. Az **ajánlat áttekintése** lapon válassza ki a **Go Live (ugrás** ) gomb alatti előnézeti hivatkozást.

1. A végpontok közötti vásárlási és beállítási folyamat ellenőrzéséhez vásárolja meg ajánlatát, amíg az előzetes verzióban is elérhető. Először is értesítse a Microsoftot egy [támogatási jegyről](https://aka.ms/marketplacesupport) , hogy ne dolgozzon fel díjat.

1. Ha az SaaS-ajánlat [a kereskedelmi Piactéri mérési szolgáltatással támogatja a mért számlázást](./partner-center-portal/saas-metered-billing.md), tekintse át és kövesse a [piactéren mért számlázási API](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)-k által részletezett ajánlott eljárásokat.

1. Tekintse át és kövesse a [Microsoft kereskedelmi piactéren elérhető SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) -betöltési API-k 2. verziójának tesztelési utasításait, és győződjön meg arról, hogy az ajánlata sikeres volt az API-kkal való integráció előtt.

1. Ha az ajánlat ellenőrzésének lépése figyelmeztetést eredményezett, megjelenik az **ajánlat áttekintése** lapon látható **ellenőrzési jelentés** hivatkozása. Mindenképpen tekintse át a jelentést, és oldja meg a problémákat, mielőtt kiválasztja az **élő indítás** gombot. Ellenkező esetben a minősítés valószínűleg sikertelen lesz, és késlelteti az ajánlatot élőben.

1. Ha az ajánlat előzetes megtekintése és tesztelése után módosításokat kell végrehajtania, szerkesztheti és újból elküldheti az új előnézet közzétételét. További információ: [meglévő ajánlat frissítése a kereskedelmi piactéren](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Ajánlat élő közzététele

Az előzetes verzióra vonatkozó összes teszt befejezése után válassza az élő adás lehetőséget, hogy az ajánlat élőben **elérhető** legyen a kereskedelmi piactéren.

   > [!TIP]
   > Ha az ajánlata már elérhető a kereskedelmi piactéren, az Ön által végzett frissítések csak akkor lépnek életbe, ha a **Go Live** lehetőséget választja.

Most, hogy az ajánlatot elérhetővé teszi a kereskedelmi piactéren, egy sor végső ellenőrzési ellenőrzést végzünk, hogy az élő ajánlat ugyanúgy legyen konfigurálva, mint az ajánlat előzetes verziója. Az ellenőrzési ellenőrzésekkel kapcsolatos további információkért lásd: [közzétételi fázis](review-publish-offer.md#publish-phase).

Az ellenőrzés befejezése után az ajánlat a piactéren lesz elérhető.

## <a name="next-steps"></a>Következő lépések

- [A partner Center kereskedelmi piactérről származó analitikai jelentések elérése](./partner-center-portal/analytics.md)
