---
title: Futásidejű kivételek diagnosztizálása az Azure Application Insights használatával | Microsoft Docs
description: Oktatóanyag az alkalmazásában előforduló futásidejű kivételek észleléséhez és diagnosztizálásához az Azure Application Insights használatával.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 1d8dfb3eaf3ea1ab901df4bef62210d28e02c3a4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383151"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Futásidejű kivételek észlelése és diagnosztizálása az Azure Application Insights segítségével

Az Azure Application Insights telemetriát gyűjt az alkalmazásából a futásidejű kivételek azonosításához és diagnosztizálásához.  Ez az oktatóanyag ismerteti a folyamatot az alkalmazására vonatkozóan.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A projekt módosítása a kivételek nyomon követésének engedélyezéséhez
> * Az alkalmazás különböző összetevőihez tartozó kivételek azonosítása
> * Egy kivétel részleteinek megtekintése
> * A kivételről készített pillanatfelvétel letöltése a Visual Studio alkalmazásba a hibakereséshez
> * A sikertelen kérelmek részleteinek elemzése a lekérdezési nyelv segítségével
> * Egy új munkaelem létrehozása a hibás kód kijavításához


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a következő munkaterhelésekkel:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Töltse le és telepítse a [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) alkalmazást.
- Engedélyezze a [Visual Studio Snapshot Debugger](../app/snapshot-debugger.md) alkalmazást.
- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../app/asp-net.md)-t. 
- Ez az oktatóanyag az alkalmazásban történt kivétel azonosításának módszerét ismerteti, ezért módosítsa a kódot a fejlesztési vagy a tesztelési környezetben, hogy létrehozzon egy kivételt. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .


## <a name="analyze-failures"></a>Hibák elemzése
Az Application Insights összegyűjti az alkalmazásában felmerült hibákat, és szemlélteti a gyakoriságukat a különböző műveleteknél, hogy Ön a legnagyobb problémát okozó hibákra koncentrálhasson.  Ezután részletesen elemezheti a hibákat, hogy megtalálja a probléma okát.   

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  
2. A **Hibák** panel megnyitásához válassza a **Hibák** elemet a **Vizsgálat** menüben, vagy kattintson a **Sikertelen kérelmek** gráfra.

    ![Sikertelen kérelmek](media/tutorial-runtime-exceptions/failed-requests.png)

3. A **Sikertelen kérelmek** panel megmutatja a sikertelen kérelmek és az érintett felhasználók számát, az alkalmazás minden egyes műveletére vonatkozóan.  Ha ezeket az információkat felhasználók szerint rendezi, megtalálhatja azokat a hibákat, amelyek a legnagyobb hatással vannak a felhasználókra.  Ebben a példában a **GET Employees/Create** és a **GET Customers/Details** elemeket érdemes megvizsgálni a hibák és az érintett felhasználók nagy száma miatt.  Egy művelet kiválasztásával további információkat tekinthet meg a műveletről a jobb oldali panelen.

    ![Sikertelen kérelmek panel](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. Az időtartomány csökkentésével ráközelíthet arra az időszakra, amelyben a hibák száma kiugróan magas.

    ![Sikertelen kérelmek ablak](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. A szűrt eredmények számát tartalmazó gombra kattintva megjeleníthetők a kapcsolódó minták. A „javasolt” minták az összes összetevőből rendelkeznek telemetriával, még akkor is, ha bármelyikben mintavételezés volt érvényben. Kattintson egy keresési eredményre a hiba részleteinek megtekintéséhez.

    ![Sikertelen kérelmek mintái](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. A sikertelen kérés részleteit mutató oldal Gantt-diagramot jelenít meg, amely megmutatja, hogy két függőségi hiba történt ebben a tranzakcióban, amelyek együtt a teljes időtartam több mint 50%-át tették ki. Ez a felület az összes olyan telemetriát megjeleníti, amely ezen műveleti azonosítóhoz kötődő hozzárendelt alkalmazások alkotóelemeire vonatkozik. [További információ az új felületről](../app/transaction-diagnostics.md). Ha kiválasztja bármelyik elemet, a jobb oldalon megjelennek az elem részletei. 

    ![Sikertelen kérelem részletei](media/tutorial-runtime-exceptions/failed-request-details.png)

7. A művelet részleteiben egy FormatException elem is látható, amely valószínűleg a hibát okozta.  Láthatja, hogy az ok egy érvénytelen irányítószám volt. A hibakeresési pillanatkép megnyitásával láthatja a kódszintű hibakeresési információkat a Visual Studióban.

    ![Kivétel részletei](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Sikertelen kód azonosítása
A Snapshot Debugger az alkalmazásában leggyakrabban előforduló kivételekről gyűjt pillanatfelvételeket, hogy segítsen éles környezetben diagnosztizálni azok alapvető okát.  A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. Ezt követően lehetősége van a forráskód hibakeresésére a pillanatkép letöltésével és a Visual Studio 2019 Enterprise-ban való megnyitásával.

1. A kivétel tulajdonságaiban kattintson a **Hibakeresési pillanatfelvétel megnyitása** elemre.
2. A **Hibakeresési pillanatfelvétel** panel a kérés hívásvermével nyílik meg.  Az egyes metódusokra kattintva megtekintheti az összes helyi változónak a kérés időpontjában rögzített értékeit.  Ebben a példában a legfelső metódustól kezdve olyan változókat láthatunk, amelyeknek nincs értéke.

    ![Hibakeresési pillanatkép](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. A **ValidZipCode** az első hívás, amely érvényes értékkel rendelkezik, és láthatjuk, hogy egy olyan, betűkkel megadott irányítószámot kaptunk, amelyet nem lehet egész számra fordítani.  Úgy tűnik, ez az a hiba, amelyet ki kell javítani.

    ![Képernyőkép, amely a helyesbíteni kívánt kód hibáját mutatja.    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. Ezután letöltheti ezt a pillanatképet a Visual studióba, ahol megtalálhatja a javítani kívánt kódot. Ehhez kattintson a **Pillanatkép letöltése** elemre.
5. A rendszer betölti a pillanatfelvételt a Visual Studióba.
6. Most már futtathat egy hibakeresési munkamenetet a Visual Studio Enterprise-ban, amely gyorsan azonosítja a kivételt okozó kód sorát.

    ![Kivétel a kódban](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Elemzési adatok használata
Az Application Insights által gyűjtött minden adatot az Azure Log Analytics tárol, amely egy részletes lekérdezési nyelvet biztosít, lehetővé téve az adatok különféle módon történő elemzését.  Arra használhatjuk ezeket az adatokat, hogy elemezzük azokat a kéréseket, amelyek a vizsgált kivételt létrehozták. 

1. Kattintson a CodeLens információra a kód felett az Application Insights által biztosított telemetria megtekintéséhez.

    ![Code](media/tutorial-runtime-exceptions/codelens.png)

1. Kattintson a **Hatás elemzése** elemre az Application Insights Analytics megnyitásához.  Több lekérdezés is található itt, amelyek részleteket biztosítanak a sikertelen kérésekről, például az érintett felhasználókról, böngészőkről és régiókról.<br><br>![A képernyőképen Application Insights ablak több lekérdezést is tartalmaz.](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Munkaelem hozzáadása
Ha az Application Insights alkalmazást egy követőrendszerhez csatlakoztatja, például az Azure DevOpshoz vagy a GitHubhoz, létrehozhat egy munkaelemet közvetlenül az Application Insightsból.

1. Térjen vissza a **Kivétel tulajdonságai** panelhez az Application Insightsban.
2. Kattintson az **Új munkaelem** elemre.
3. A megnyíló **Új munkaelem** panelen automatikusan megjelennek a kivétel részletei.  Bármilyen egyéb információt hozzáadhat mentés előtt.

    ![Új munkaelem](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Következő lépések
Most már megtanulta, hogyan azonosíthatja a futásidejű kivételeket. Térjen át a következő oktatóanyagra, hogy megtanulja, hogyan azonosíthatja és diagnosztizálhatja a teljesítménybeli problémákat.

> [!div class="nextstepaction"]
> [Teljesítménybeli problémák azonosítása](./tutorial-performance.md)

