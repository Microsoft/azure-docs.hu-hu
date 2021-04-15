---
title: Incidensek kivizsgálása Azure Sentinel| Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Sentinel riasztási szabályokat a hozzárendelni és kivizsgálni képes incidensek létrehozásához.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2021
ms.author: yelevin
ms.openlocfilehash: 8980a8920b4f41f5a8e6afe106415032eef2055b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375838"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Oktatóanyag: Incidensek kivizsgálása Azure Sentinel

> [!IMPORTANT]
> A vizsgálati gráf mostantól **általánosan elérhető.** 

Ez az oktatóanyag segítséget nyújt az incidensek kivizsgálásában Azure Sentinel. Miután csatlakoztatta az adatforrásokat a Azure Sentinel, szeretne értesítést látni, ha valami gyanús történik. Ennek lehetővé teszi a Azure Sentinel, hogy speciális riasztási szabályokat hozzon létre, amelyek hozzárendelheti és kivizsgálhatja az incidenseket.

Ez a cikk a következővel foglalkozik:
> [!div class="checklist"]
> * Incidensek vizsgálata
> * A vizsgálati gráf használata
> * Reagálás a fenyegetésekre

Egy incidens több riasztást is tartalmazhat. Egy adott vizsgálat összes releváns bizonyítékának összesítése. A rendszer az Elemzés oldalon létrehozott elemzési  szabályok alapján hoz létre incidenst. A riasztásokkal kapcsolatos tulajdonságok, például a súlyosság és az állapot az incidens szintjén vannak beállítva. Miután tudatta Azure Sentinel, hogy milyen típusú fenyegetéseket keres, és hogyan találhatja meg őket, az incidensek vizsgálatának segítségével figyelheti az észlelt fenyegetéseket.

## <a name="prerequisites"></a>Előfeltételek
- Az incidenst csak akkor tudja kivizsgálni, ha az entitásleképezés mezőit használta az elemzési szabály beállításakor. A vizsgálati gráf megköveteli, hogy az eredeti incidenst tartalmazzon entitásokat.

- Ha van egy vendégfelhasználója, aki incidenseket szeretne hozzárendelni, a felhasználóhoz címtárolvasó szerepkört kell rendelni az Azure AD-bérlőben. [](../active-directory/roles/permissions-reference.md#directory-readers) A normál (nem vendég) felhasználók alapértelmezés szerint ehhez a szerepkörhöz vannak hozzárendelve.

## <a name="how-to-investigate-incidents"></a>Incidensek vizsgálata

1. Válassza **az Incidents (Incidensek) lehetőséget.** Az **Incidensek** lapon megtekintheti, hogy hány incidense van, hány nyitott, hány folyamatban lévőre van beállítva, és hány van lezárva. Minden incidensnél láthatja a beesés idejét és állapotát. A súlyosság alapján döntse el, hogy mely incidenseket kell elsőként kezelnie.

    ![Incidens súlyosságának megtekintése](media/tutorial-investigate-cases/incident-severity.png)

1. Az incidenseket szükség szerint szűrheti, például állapot vagy súlyosság szerint.

1. A vizsgálat megkezdéséhez válasszon ki egy adott incidenst. A jobb oldalon részletes információkat láthat az incidensről, beleértve annak súlyosságát, az érintett entitások számának összegzését, az incidenst kiváltó nyers eseményeket, valamint az incidens egyedi azonosítóját.

1. Az incidensben található riasztások és entitások további  részleteinek megtekintéséhez válassza a Teljes adatok megtekintése az incidens oldalon lehetőséget, és tekintse át az incidens adatait összegő megfelelő lapokat. 

    ![Riasztás részleteinek megtekintése](media/tutorial-investigate-cases/incident-timeline.png)

    Például:

    - Az **Idősor lapon** tekintse át az incidensben lévő riasztások és könyvjelzők idővonalát, ami segíthet rekonstruálni a támadói tevékenység idővonalát.
    - A **Riasztások lapon** tekintse át magát a riasztást. A riasztásra vonatkozó összes releváns információt láthatja – a riasztást kiváltó lekérdezést, a lekérdezésenként visszaadott eredmények számát, valamint a forgatókönyveket a riasztások alapján futtathatja. Az incidens további részletezésében válassza ki az Események **számát.** Ez megnyitja az eredményeket generáló lekérdezést és a Log Analyticsben a riasztást kiváltó eseményeket. 
    - Az **Entitások lapon** láthatja a riasztási szabály definíciójának részeként leképezett összes entitást.

1. Ha aktívan vizsgál egy incidenst, jó ötlet, ha az incidens  állapotát Folyamatban állapotra állítsa, amíg be nem zárja.

1. Az incidensek hozzárendelhetőek egy adott felhasználóhoz. Minden incidenshez hozzárendelhet egy tulajdonost az Incidens tulajdonosa **mező beállításával.** Minden incidens hozzárendeletlenként kezdődik. Megjegyzéseket is hozzáadhat, hogy más elemzők is megértsék, mit vizsgált meg, és milyen aggodalmai vannak az incidensekkel kapcsolatban.

    ![Incidens hozzárendelése a felhasználóhoz](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Válassza **a Vizsgálat** lehetőséget a vizsgálati térkép megtekintéséhez.

## <a name="use-the-investigation-graph-to-deep-dive"></a>A vizsgálati gráf használata a mély leásáshoz

A vizsgálati gráf lehetővé teszi az elemzők számára, hogy az egyes vizsgálatokhoz megfelelő kérdéseket tenek fel. A vizsgálati gráf segít a lehetséges biztonsági fenyegetések hatókörének és kiváltó okának azonosításában azáltal, hogy a releváns adatokat az érintett entitásokkal korrelál. Mélyebbre áshat, és megvizsgálhatja a gráfon megjelenő entitásokat, ha kiválasztja azt, és kiválasztja a különböző bővítési lehetőségeket.  
  
A vizsgálati gráf a következőt biztosítja:

- **Vizuális környezet nyers adatokból:** Az élő, vizuális gráf a nyers adatokból automatikusan kinyert entitáskapcsolatokat jeleníti meg. Így könnyedén láthatja a különböző adatforrások kapcsolatait.

- **Teljes vizsgálati hatókör felderítése:** Bővítse ki a vizsgálati hatókört a beépített feltárási lekérdezésekkel, hogy feltárja a biztonsági incidensek teljes hatókörét.

- **Beépített vizsgálati lépések:** Előre meghatározott feltárási lehetőségekkel győződjön meg arról, hogy a megfelelő kérdéseket teszi fel egy fenyegetéssel szemben.

A vizsgálati gráf használata:

1. Válasszon ki egy incidenst, majd válassza a **Vizsgálat lehetőséget.** Ezzel a vizsgálati gráfra kerül. A gráf a riasztáshoz közvetlenül kapcsolódó entitások és a további csatlakoztatott erőforrások szemléltető térképét biztosítja.

   > [!IMPORTANT] 
   > - Csak akkor tudja kivizsgálni az incidenst, ha az entitásleképezés mezőit használta az elemzési szabály beállításakor. A vizsgálati gráf megköveteli, hogy az eredeti incidenst tartalmazzon entitásokat.
   >
   > - Azure Sentinel jelenleg a legfeljebb **30 napos incidensek vizsgálatát támogatja.**

   ![Térkép megtekintése](media/tutorial-investigate-cases/map1.png)

1. Válasszon ki egy entitást az **Entitások panel megnyitásához,** hogy áttekintheti az entitásra vonatkozó információkat.

    ![Entitások megtekintése a térképen](media/tutorial-investigate-cases/map-entities.png)
  
1. Bontsa ki a vizsgálatot úgy, hogy a kurzort az egyes entitások fölé egeret mutatva felfedi a biztonsági szakértők és elemzők által entitástípusonként megtervezett kérdések listáját a vizsgálat elmélyítése érdekében. Ezeket a lehetőségeket feltáró **lekérdezésnek hívjuk.**

    ![További részletek](media/tutorial-investigate-cases/exploration-cases.png)

   Egy számítógépen például kérhet kapcsolódó riasztásokat. Ha egy feltárási lekérdezést választ, az eredményül kapott entitek visszakerülnek a gráfba. Ebben a példában a **Kapcsolódó riasztások lehetőség kiválasztásával** a következő riasztásokat adhatja vissza a diagramon:

    ![Kapcsolódó riasztások megtekintése](media/tutorial-investigate-cases/related-alerts.png)

1. Minden egyes feltárási lekérdezésnél kiválaszthatja a nyers eseményeredmények és a Log Analyticsben használt lekérdezés megnyitásának beállítását az **Események lehetőség \> kiválasztásával.**

1. Az incidenst a grafikon párhuzamos idővonallal mutatja be.

    ![Idővonal megtekintése a térképen](media/tutorial-investigate-cases/map-timeline.png)

1. Vigye az egérmutatót az idővonal fölé, hogy lássa, mely dolgok történtek a grafikonon.

    ![Idővonal használata a térképen a riasztások vizsgálatához](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Incidens lezárása

Miután megoldott egy adott incidenst (például amikor a vizsgálat elérte a következtetését), az incidens állapotát Lezártra kell **állítania.** Ha így történik, a rendszer megkéri, hogy sorolja be az incidenst a bezárásának okának megadásával. Ez a lépés kötelező. Kattintson **a Besorolás kiválasztása** elemre, és válasszon egyet a következők közül a legördülő listából:

- Valós pozitív – gyanús tevékenység
- Jóindulatú pozitív – gyanús, de nem váratlan
- Álpozitív – helytelen riasztási logika
- Álpozitív – helytelen adatok
- Meghatározatlan

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Képernyőkép a Besorolás kiválasztása listában elérhető besorolásokról.":::

A megfelelő besorolás kiválasztása után adjon hozzá leíró szöveget a Megjegyzés **mezőben.** Ez akkor lehet hasznos, ha vissza kell hivatkozni erre az incidensre. Ha **végzett,** kattintson az Alkalmaz gombra, és az incidens lezárul.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan vizsgál meg incidenseket a Azure Sentinel. Folytassa azzal az oktatóanyagot, amelyből [megtudhatja, hogyan reagálhat a fenyegetésekre automatizált forgatókönyvekkel.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Reagálás a fenyegetésekre](tutorial-respond-threats-playbook.md) adott válaszok automatizálása érdekében.

