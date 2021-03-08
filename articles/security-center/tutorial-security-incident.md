---
title: Riasztási válasz oktatóanyaga – Azure Security Center
description: Ebből az oktatóanyagból megtudhatja, hogyan osztályozhatja a biztonsági riasztásokat, és hogyan állapíthatja meg a riasztások gyökerének okát & hatókörét.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 41f96202bffc0169be9705cd7e67c1eedbecbc05
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453444"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Oktatóanyag: Biztonsági riasztások osztályozása, kivizsgálása és a riasztásokra való válaszadás
Security Center folyamatosan elemzi a hibrid Felhőbeli számítási feladatokat a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztessen a Felhőbeli erőforrásaiban esetlegesen rosszindulatú tevékenységekre. A riasztásokat más biztonsági termékekből és szolgáltatásokból is integrálhatja Security Centerba. A riasztások beadását követően gyors beavatkozásra van szükség a lehetséges biztonsági problémák kivizsgálásához és szervizeléséhez. 

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Biztonsági riasztások osztályozása
> * Biztonsági riasztás vizsgálata a kiváltó ok okának megállapításához
> * Válaszadás a biztonsági riasztásokra, és az alapvető ok enyhítése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban szereplő funkciók átlépéséhez engedélyeznie kell az Azure Defender használatát. Az Azure Defender szolgáltatás díjmentesen kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). A gyors üzembe helyezés Security Center végigvezeti Önt a verziófrissítés [lépésein](security-center-get-started.md) .


## <a name="triage-security-alerts"></a>Biztonsági riasztások osztályozása
A Security Center segítségével minden biztonsági riasztást egy egyesített nézetben tekinthet meg. A biztonsági riasztások az észlelt tevékenység súlyossága alapján vannak rangsorolva. 

A riasztások osztályozása a **biztonsági riasztások** lapról:

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center biztonsági riasztások listája":::

Ezen a lapon áttekintheti a környezetében található aktív biztonsági riasztásokat, hogy eldöntse, melyik riasztást kell először kivizsgálni.

Ha a riasztások súlyossága alapján osztályozásakor a riasztások fontosságát, a riasztásokat az első nagyobb súlyossággal kell kezelni. További tudnivalók a riasztások súlyosságáról: [Hogyan vannak besorolva a riasztások?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Azure Security Center csatlakozhat a legnépszerűbb SIEM-megoldásokhoz, beleértve az Azure Sentinelt is, és a riasztásokat a választott eszköztől használhatja. További információ a [stream-riasztásokról a Siem, a SOAR vagy az IT Service Management megoldáshoz](export-to-siem.md).


## <a name="investigate-a-security-alert"></a>Biztonsági riasztás vizsgálata

Ha eldöntötte, hogy melyik riasztást először vizsgálja meg:

1. Válassza ki a kívánt riasztást.
1. A riasztás áttekintése lapon válassza ki az elsőként kivizsgálni kívánt erőforrást.
1. Indítsa el a vizsgálatot a bal oldali panelen, amely a biztonsági riasztással kapcsolatos magas szintű információkat jeleníti meg.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="A riasztás részletei oldal bal oldali ablaktáblája kiemelve a magas szintű információkat":::

    Az ablaktábla a következőket jeleníti meg:
    - Riasztás súlyossága, állapota és tevékenységi ideje
    - A észlelt pontos tevékenységet ismertető Leírás
    - Érintett erőforrások
    - A tevékenység leölési lánca a MITRE ATT&a CK matrixon

1. A gyanús tevékenység kivizsgálásához segítséget nyújtó részletesebb információkat a **riasztás részletei** lapon tekintheti meg.

1. Ha áttekintette az ezen a lapon található információkat, lehetséges, hogy a válasz folytatásához elég. Ha további részletekre van szüksége:

    - Forduljon az erőforrás-tulajdonoshoz, és ellenőrizze, hogy az észlelt tevékenység hamis pozitív-e.
    - Vizsgálja meg a megtámadott erőforrás által létrehozott nyers naplókat

## <a name="respond-to-a-security-alert"></a>Válaszadás biztonsági riasztásra
Miután kivizsgálta a riasztást, és megértette a hatókörét, választ kaphat a biztonsági riasztásokra Azure Security Centeron belül:

1.  Az ajánlott válaszok megjelenítéséhez nyissa meg a **Take Action (művelet végrehajtása** ) lapot.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Biztonsági riasztások művelet lapja" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  A probléma megoldásához szükséges manuális vizsgálat lépéseiért tekintse át a **fenyegetés enyhítése** szakaszt.
1.  Ha szeretné megerősíteni az erőforrásokat, és meg szeretné akadályozni a jövőbeli támadásokat, javítsa ki a biztonsági javaslatokat a **jövőbeni támadások megelőzése** szakaszban.
1.  A logikai alkalmazások automatikus válaszokkal történő elindításához használja az **trigger automatikus válasza** szakaszt.
1.  Ha az észlelt tevékenység *nem* rosszindulatú, akkor a **hasonló riasztások mellőzése** szakaszban letilthatja az ilyen típusú riasztásokat.

1.  Ha befejezte a vizsgálatot a riasztásban, és a megfelelő módon válaszolt, módosítsa az állapotot elvetett **értékre**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Riasztás állapotának beállítása":::

    Ezzel eltávolítja a riasztást a fő riasztások listájáról. A riasztások listája lapon található szűrő használatával megtekintheti az összes **elhagyott** állapottal rendelkező riasztást.

1.  Javasoljuk, hogy visszajelzést nyújtson a Microsoftnak a riasztásról:
    1. A riasztás **hasznosként** vagy **nem hasznosként** való megjelölése.
    1. Válasszon ki egy okot, és adjon hozzá egy megjegyzést.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Visszajelzés küldése a Microsoftnak a riasztás hasznosságáról":::

    > [!TIP]
    > Áttekintjük a visszajelzéseit, hogy javítsuk az algoritmusokat, és jobb biztonsági riasztásokat nyújtsanak.

## <a name="end-the-tutorial"></a>Az oktatóanyag befejezése

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, tartsa meg az automatikus kiépítés és az Azure Defender használatát. 

Ha nem folytatja a folytatást, vagy le szeretné tiltani valamelyik funkciót:

1. Térjen vissza a Security Center főmenüre, és válassza a **díjszabás és beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. A visszalépéshez válassza az **Azure Defender kikapcsolva** lehetőséget.
1. Az automatikus kiépítés letiltásához nyissa meg az **adatgyűjtés** lapot, és állítsa be az **automatikus kiépítés** **beállítást.**
1. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Log Analytics ügynököt olyan Azure-beli virtuális gépekről, amelyek már rendelkeznek ügynökkel. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>Következő lépések
Ebből az oktatóanyagból megtudhatta, hogyan használhatók a biztonsági riasztásokra reagáló Security Center funkciók. Kapcsolódó anyagok esetében lásd:

- [Válaszadás Key Vaulthoz készült Azure Defender-riasztásokra](defender-for-key-vault-usage.md)
- [Biztonsági riasztások - – referencia-útmutató](alerts-reference.md)
- [Bevezetés az Azure Defender használatába](azure-defender.md)
