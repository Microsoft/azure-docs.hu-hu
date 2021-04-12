---
title: Teljesítménybeli problémák diagnosztizálása az Azure Application Insights használatával | Microsoft Docs
description: Oktatóanyag az alkalmazásában előforduló teljesítménybeli problémák észleléséhez és diagnosztizálásához az Azure Application Insights használatával.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: 36a581cb958639f4e9d88a8828b341ef1a1919d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383218"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Teljesítménybeli problémák észlelése és diagnosztizálása az Application Insights segítségével

Az Azure Application Insights telemetriát gyűjt az alkalmazásából a teljesítmény és a működés elemzésének segítése érdekében.  Ezt az információt arra használhatja, hogy azonosítsa a potenciális problémákat, vagy hogy megtalálja azokat a fejlesztési lehetőségeket az alkalmazásban, amelyek a legnagyobb hatással lennének a felhasználókra.  Ez az oktatóanyag az alkalmazás kiszolgáló-összetevőinek, valamint az ügyfél teljesítményének elemzési módszereit ismerteti.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kiszolgálóoldali műveletek teljesítményének azonosítása
> * Kiszolgálói műveletek elemzése a lassú teljesítmény okának megállapítása érdekében
> * A leglassabb ügyféloldali művelet azonosítása
> * Az oldalmegtekintések részleteinek elemzése a lekérdezési nyelv segítségével


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a következő munkaterhelésekkel:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../app/asp-net.md)-t.
- [Engedélyezze az Application Insights profilkészítőt](../app/profiler.md#installation) az alkalmazásához.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .

## <a name="identify-slow-server-operations"></a>Lassú kiszolgálói műveletek azonosítása
Az Application Insights teljesítményadatokat gyűjt az alkalmazás különböző műveleteiről. A leghosszabb időtartamú műveletek azonosításával diagnosztizálhatja a potenciális problémákat vagy hatékonyabbá teheti a folyamatban lévő fejlesztéseit, ezáltal pedig javíthat az alkalmazás általános teljesítményén.

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  
1. A **Teljesítmény** panel megnyitásához válassza a **Teljesítmény** elemet a **Vizsgálat** menüben, vagy kattintson a **Kiszolgáló válaszideje** gráfra.

    ![Teljesítmény](media/tutorial-performance/1-overview.png)

2. A **Teljesítmény** panel megjeleníti az alkalmazás egyes műveleteinek számát és átlagos időtartamát.  Ezt az információt arra használhatja, hogy azonosítsa azokat a műveleteket, amelyek a legnagyobb hatással vannak a felhasználókra. Ebben a példában a **GET Customers/Details** és a **GET Home/Index** elemeket érdemes megvizsgálni a viszonylagosan hosszú időtartamuk és a hívások magas száma miatt.  Más műveleteknek hosszabb időtartamuk lehet, viszont ritkán kaptak hívást, ezért a javításuk minimális hatást eredményezne.  

    ![Teljesítmény-kiszolgáló panel](media/tutorial-performance/2-server-operations.png)

3. Ez a grafikon jelenleg a kijelölt műveletek átlagos időtartamát mutatja. A teljesítményproblémákat a 95. percentilisre váltva találhatja meg. Adja hozzá azokat a műveleteket, amelyekre kíváncsi. Ehhez rögzítse őket a gráfhoz.  Itt látszik, hogy van néhány csúcsérték, amelyeket érdemes megvizsgálni.  Tovább szűkítheti az eredmények körét, ha csökkenti a gráf időkeretét.

    ![Műveletek rögzítése](media/tutorial-performance/3-server-operations-95th.png)

4.  A jobb oldali teljesítmény panelen a kiválasztott művelet különböző kéréseihez tartozó időtartamok eloszlását láthatja.  Szűkítse a keretet úgy, hogy a 95. percentilisnél kezdődjön. A „3 legnagyobb függőség” megállapításkártya egyszerűen megmutatja, hogy a külső függőségek valószínűleg hozzájárulnak a tranzakciók lassúságához.  Kattintson a minták számát tartalmazó gombra a minták listájának megtekintéséhez. A listáról kiválaszthatja bármelyik mintát, és megtekintheti a tranzakciók részleteit.

5.  Első ránézésre is megállapítható, hogy a Fabrikamaccount Azure-tábla a felelős a tranzakció teljes időtartamának legnagyobb részéért. Láthatja továbbá, hogy egy kivétel miatt meghiúsult. Ha a lista bármelyik elemére kattint, a jobb oldalon megjelennek az elem részletei. [További információ a tranzakciódiagnosztikai felületről](../app/transaction-diagnostics.md)

    ![Végpontok közötti részletek](media/tutorial-performance/4-end-to-end.png)
    

6.  A [**Profiler**](../app/profiler-overview.md) segítséget nyújt a kódok szintjének diagnosztizálásához azáltal, hogy megjeleníti a művelethez és az egyes lépésekhez szükséges időponthoz tartozó tényleges kódot. Előfordulhat, hogy bizonyos műveletek nem hagynak nyomot, mivel a profilkészítő időszakosan fut.  Idővel több műveletnek lehet nyoma.  Kattintson a **Profilkészítői adatok** elemre a profilkészítő a műveleten való futtatásának elindításához.
5.  A nyom megjeleníti az egyes műveletekhez tartozó eseményeket, így diagnosztizálhatja a művelet időtartamáért felelős kiváltó okot.  Kattintson az egyik leghosszabb időtartamot megjelenítő példára.
6.  Kattintson a **gyors elérési út** elemre, hogy kiemelje a művelet teljes időtartamára vonatkozó események megadott elérési útját.  Ebben a példában azt láthatja, hogy a leglassabb hívás a *FabrikamFiberAzureStorage.GetStorageTableData* metódusból származik. A legtöbb időt igénybe vevő metódus a *CloudTable.CreateIfNotExist*. Ha ez a kódsor lefut minden alkalommal, amikor a függvény meghívása megtörténik, az felesleges hálózati hívásokat és processzorhasználatot eredményez. A kód kijavításának legjobb módja az, hogy ha ezt a sort egy olyan indítási metódusba helyezi, amely csak egyszer fut le.

    ![Profilkészítő részletei](media/tutorial-performance/5-hot-path.png)

7.  A **Teljesítménnyel kapcsolatos tipp** a képernyő tetején megerősíti azt a következtetést, hogy a hosszú időtartamot a várakozás okozza.  Kattintson a **várakozás** hivatkozásra a különböző események értelmezéséről szóló dokumentáció megnyitásáért.

    ![Teljesítménnyel kapcsolatos tipp](media/tutorial-performance/6-perf-tip.png)

8.   További elemzéshez kattintson a **nyomkövetés letöltése** lehetőségre a nyomkövetés letöltéséhez. Ezeket az adatlapokat a [perfview eszköz](https://github.com/Microsoft/perfview#perfview-overview)használatával tekintheti meg.

## <a name="use-logs-data-for-server"></a>A kiszolgálók naplófájljainak használata
 A naplók részletes lekérdezési nyelvet biztosítanak, amely lehetővé teszi az Application Insights által összegyűjtött összes adatok elemzését. Ezzel a funkcióval részletes elemzéseket végezhet a kérések és a teljesítmény adatairól.

1. Térjen vissza a művelet részletei panelre, és kattintson a naplók ![ ikon](media/tutorial-performance/app-viewinlogs-icon.png)**nézet a naplókban (Analitika)** elemre.

2. A naplók megnyílik egy lekérdezéssel a panel egyes nézeteinél.  Ezeket a lekérdezéseket futtathatja alapértelmezetten, vagy módosíthat rajtuk az igényei szerint.  Az első lekérdezés megjeleníti ennek a műveletnek a teljes időtartamát.

    ![naplók lekérdezése](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Lassú ügyfélműveletek azonosítása
Amellett, hogy azonosítja a kiszolgálói folyamatokat az optimalizálás érdekében, az Application Insights az ügyfélböngészők szempontjából is képes elemzést végezni.  Így megtalálhatja a javítási lehetőségeket az ügyfél összetevői számára, és különböző böngészők vagy helyek problémáit is megtalálhatja.

1. A **vizsgálat** **területen válassza a** böngésző **teljesítménye** lehetőséget, vagy a jobb felső sarokban lévő kiszolgáló/böngésző váltógomb lehetőségre kattintva nyissa meg a böngésző teljesítményének összegzése **elemet a** **vizsgálat elemnél, és váltson** a **böngésző lapra.** Ez egy vizuális összefoglalást nyújt az alkalmazása különböző telemetriáiról a böngésző szempontjából.

    ![Böngésző összefoglalása](media/tutorial-performance/8-browser.png)

2. Válassza ki az egyik művelet nevét, majd a jobb alsó sarokban kattintson a kék minták gombra, és válasszon ki egy műveletet. Ekkor megjelenik a végpontok közötti tranzakció részletei, a jobb oldalon pedig megtekintheti az **oldal nézetének tulajdonságait**. Ez lehetővé teszi, hogy megtekintse a lapot kérő ügyfél adatait, beleértve a böngésző típusát és helyét. Ez az információ segítheti abban, hogy megállapítsa, vannak-e az adott ügyféltípusokra jellemző teljesítményproblémák.

    ![Oldalmegtekintés](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Az ügyfél naplófájljainak használata
A kiszolgáló teljesítményére gyűjtött adatokhoz hasonlóan a Application Insights az összes ügyfél-adat számára elérhetővé teszi a mélyreható elemzést a naplók használatával.

1. Térjen vissza a böngésző összegzéséhez, és kattintson a naplók ![ ikon ](media/tutorial-performance/app-viewinlogs-icon.png) **nézet a naplókban (Analitika)** elemre.

2. A naplók megnyílik egy lekérdezéssel a panel egyes nézeteinél. Az első lekérdezés a különböző oldalmegtekintések időtartamát jeleníti meg az idő függvényében.

    ![Naplók lekérdezése](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Következő lépések
Most már megtanulta, hogyan azonosíthatja a futásidejű kivételeket. Térjen át a következő oktatóanyagra, hogy megtanulja, hogyan hozhat létre riasztásokat a hibákhoz.

> [!div class="nextstepaction"]
> [Alkalmazásállapot-riasztás](./tutorial-alert.md)

