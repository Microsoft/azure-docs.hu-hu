---
title: Incidensek vizsgálata az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan vizsgálja ki az incidenseket az Azure Sentinel használatával.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 026689b68244052c3ad7cb50cd90d7319b6760ef
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71229405"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Oktatóanyag: Incidensek vizsgálata az Azure Sentineltel

> [!IMPORTANT]
> A vizsgálati gráf jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ez az oktatóanyag segítséget nyújt az incidensek vizsgálatához az Azure Sentinel használatával. Miután csatlakoztatta az adatforrásokat az Azure Sentinel szolgáltatáshoz, értesítést szeretne kapni, ha valamilyen gyanús esemény történik. Ennek lehetővé tételéhez az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre, amelyek a hozzárendelhető és kivizsgálható incidenseket eredményeznek.

Ez a cikk a következőket ismerteti:
> [!div class="checklist"]
> * Incidensek vizsgálata
> * A vizsgálati gráf használata
> * Reagálás a fenyegetésekre

Egy incidens több riasztást is tartalmazhat. Ez egy adott vizsgálatra vonatkozó összes releváns bizonyíték összesítése. Az incidens az **elemzés** lapon létrehozott analitikus szabályok alapján jön létre. A riasztásokhoz kapcsolódó tulajdonságok, például a súlyosság és az állapot az incidens szintjén vannak megadva. Miután engedélyezte az Azure Sentinel számára, hogy megtudja, milyen veszélyforrásokat keres, és hogyan keresheti meg őket, figyelheti az észlelt fenyegetéseket az incidensek kivizsgálásával.

## <a name="prerequisites"></a>Előfeltételek
Az incidens kivizsgálása csak akkor lehetséges, ha az analitikus szabály beállításakor az entitás-hozzárendelési mezőket használta. A vizsgálati gráf megköveteli, hogy az eredeti incidens entitásokat tartalmazzon.

## <a name="how-to-investigate-incidents"></a>Incidensek vizsgálata

1. Válassza az **incidensek**lehetőséget. Az **incidensek** oldalon megtudhatja, hogy hány incidens van, hány van nyitva, hányan van **folyamatban**, és hány lezárult. Minden incidensnél láthatja a bekövetkezett időt, valamint az incidens állapotát. Tekintse meg a súlyosságot, hogy eldöntse, melyik incidenst kell először kezelni.

    ![Incidens súlyosságának megtekintése](media/tutorial-investigate-cases/incident-severity.png)

1. Igény szerint szűrheti az incidenseket, például az állapot vagy a súlyosság alapján.

1. A vizsgálat megkezdéséhez válasszon ki egy adott incidenst. A jobb oldalon megtekintheti az incidens részletes információit, beleértve annak súlyosságát, az érintett entitások számának összefoglalását, az eseményt kiváltó nyers eseményeket és az incidens egyedi AZONOSÍTÓját.

1. Az incidensben lévő riasztásokkal és entitásokkal kapcsolatos további részletek megtekintéséhez válassza az incidens lapon a **teljes részletek megtekintése** lehetőséget, és tekintse át az incidens információit összefoglaló kapcsolódó lapokat. A **riasztások** lapon tekintse át magát a riasztást. Láthatja a riasztással kapcsolatos összes releváns információt – a riasztást kiváltó lekérdezést, a lekérdezésekben visszaadott eredmények számát, valamint a riasztásokra vonatkozó forgatókönyvek futtatásának lehetőségét. Az incidensek még mélyebb részletezéséhez válassza ki az **események**számát. Ekkor megnyílik a lekérdezés, amely létrehozta az eredményeket, és a riasztást kiváltó eseményeket Log Analyticsban. Az **entitások** lapon a riasztási szabály definíciójának részeként leképezett összes entitás látható.

    ![Riasztás részleteinek megtekintése](media/tutorial-investigate-cases/alert-details.png)

1. Ha aktívan vizsgálja az incidenst, érdemes lehet az incidens állapotát **előre** beállítani, amíg be nem zárjuk.

1. Az incidensek egy adott felhasználóhoz rendelhetők hozzá. Minden incidenshez hozzárendelhet egy tulajdonost az **incidens tulajdonosa** mező beállításával. Az összes incidens nincs hozzárendelve. Megjegyzéseket is hozzáadhat, hogy más elemzők megértsék a megvizsgált személyeket és az incidensek körét.

    ![Incidens társítása a felhasználóhoz](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. A vizsgálati Térkép **megtekintéséhez válassza a vizsgálat lehetőséget** .

## <a name="use-the-investigation-graph-to-deep-dive"></a>A vizsgálati gráf részletes használata

A vizsgálati gráf lehetővé teszi, hogy az elemzők a megfelelő kérdéseket tegyenek fel az egyes vizsgálatokhoz. A vizsgálati gráf segítségével megismerheti a hatókört, és azonosíthatja a lehetséges biztonsági fenyegetéseket, ha a releváns adatokat bármilyen érintett entitással korrelálja. A grafikonon bemutatott összes entitást kiválaszthatja, és kiválaszthatja a különböző bővítési lehetőségek között.  
  
A vizsgálati gráf a következőket biztosítja:

- **Vizualizációs környezet a nyers adatokból**: Az élő, vizuális gráf megjeleníti a nyers adatokból automatikusan kinyert entitás-kapcsolatokat. Ez lehetővé teszi a különböző adatforrások közötti kapcsolatok egyszerű megtekintését.

- **Teljes vizsgálat hatókörének felderítése**: Kiterjesztheti a vizsgálat hatókörét a beépített felderítési lekérdezésekkel, hogy felhasználhassa a szabálysértés teljes körét.

- **Beépített vizsgálat lépései**: Az előre meghatározott feltárási lehetőségek használatával győződjön meg arról, hogy a megfelelő kérdéseket kéri fel a fenyegetés szem előtt.

A vizsgálati gráf használata:

1. Válasszon ki egy incidenst, majd kattintson a **vizsgálat**elemre. Ezzel elvégzi a vizsgálati gráfot. A gráf szemléltető térképet biztosít a riasztáshoz közvetlenül kapcsolódó entitásokhoz, és minden további kapcsolódó erőforrást.

   > [!IMPORTANT] 
   > Az incidens kivizsgálása csak akkor lehetséges, ha az analitikus szabály beállításakor az entitás-hozzárendelési mezőket használta. A vizsgálati gráf megköveteli, hogy az eredeti incidens entitásokat tartalmazzon.

   ![Térkép megtekintése](media/tutorial-investigate-cases/map1.png)

1. Válasszon ki egy entitást az **entitások** panel megnyitásához, hogy áttekintse az adott entitás információit.

    ![Térképbeli entitások megtekintése](media/tutorial-investigate-cases/map-entities.png)
  
1. Kiterjesztheti a vizsgálatot úgy, hogy az egyes entitások fölé viszi azokat a kérdéseket, amelyeket a biztonsági szakértők és az elemzők által a vizsgálat elmélyítése érdekében hoztak létre. Ezeket a beállításokat nevezzük a **lekérdezéseknek**.

    ![További részletek](media/tutorial-investigate-cases/exploration-cases.png)

   A számítógépen például a kapcsolódó riasztások is igényelhetők. Ha kijelöl egy feltárási lekérdezést, a rendszer visszaadja az eredményül kapott jogosultságokat a gráfhoz. Ebben a példában a **kapcsolódó riasztások** kiválasztása a következő riasztásokat adta vissza a gráfba:

    ![Kapcsolódó értesítések megtekintése](media/tutorial-investigate-cases/related-alerts.png)

1. Az egyes felderítési lekérdezések esetében kiválaszthatja a nyers események eredményeinek és a Log Analytics használt lekérdezésnek az **események\>** lehetőséget választva történő megnyitásának lehetőségét.

1. Az incidens megismerése érdekében a gráf párhuzamos idővonalat biztosít.

    ![Térkép idővonalának megtekintése](media/tutorial-investigate-cases/map-timeline.png)

1. Vigye az egérmutatót az ütemterv fölé, és tekintse meg, hogy mely dolgokon történt a gráf az adott időpontban.

    ![A riasztások vizsgálatához használja a Térkép idővonalát](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan kezdheti el az incidensek vizsgálatát az Azure Sentinel használatával. Folytassa az Oktatóanyaggal, hogy [miként reagálhat a fenyegetésekre automatizált forgatókönyvek használatával](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Válaszoljon a fenyegetésekre](tutorial-respond-threats-playbook.md) , hogy automatizálja a fenyegetésekre adott válaszokat.

