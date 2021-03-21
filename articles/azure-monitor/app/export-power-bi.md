---
title: Exportálás Power BI az Azure Application Insightsból | Microsoft Docs
description: Az elemzési lekérdezések a Power BIban is megjeleníthetők.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 4bdae09d193d5ae9c59fe9352e07b8219a76841d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584308"
---
# <a name="feed-power-bi-from-application-insights"></a>Hírcsatorna-Power BI Application Insights
A [Power bi](https://www.powerbi.com/) egy olyan üzleti eszköz, amely megkönnyíti az adatok elemzését és az elemzések megosztását. A gazdag irányítópultok minden eszközön elérhetők. Több forrásból is egyesítheti az adatait, beleértve az [Azure Application Insightsból](./app-insights-overview.md)származó elemzési lekérdezéseket is.

Az Application Insights-adatPower BIek exportálásának három módja van:

* [**Elemzési lekérdezések exportálása**](#export-analytics-queries). Ez az elsődleges módszer. Írja le a kívánt lekérdezést, és exportálja Power BIba. Ezt a lekérdezést egy irányítópulton helyezheti el, valamint bármilyen más adattal.
* [**Folyamatos exportálás és Azure stream Analytics**](./export-stream-analytics.md). Ez a módszer akkor hasznos, ha hosszú ideig szeretné tárolni az adatait. Ha nem rendelkezik kiterjesztett adatmegőrzési követelménnyel, használja az export Analytics lekérdezési módszert. A folyamatos exportálás és a Stream Analytics több munkaterhelést is magában foglal, és további tárterületet igényel.
* **Power bi adapter**. A diagramok készlete előre definiálva van, de bármilyen más forrásból is hozzáadhat saját lekérdezéseket.

> [!NOTE]
> A Power BI adapter már **elavult**. A megoldás előre definiált diagramjait statikus, nem szerkeszthető lekérdezések töltik fel. Nem tudja szerkeszteni ezeket a lekérdezéseket, és az adatok bizonyos tulajdonságaitól függően lehetséges, hogy a kapcsolódás Power BI sikeres, de az adatok nem lesznek feltöltve. Ez az hardcoded-lekérdezésben beállított kizárási feltételek miatt fordul elő. Habár ez a megoldás továbbra is működhet bizonyos ügyfelek esetében, az adapter rugalmasságának hiánya miatt az ajánlott megoldás az [**export Analytics lekérdezési**](#export-analytics-queries) funkciójának használata.

## <a name="export-analytics-queries"></a>Analytics-lekérdezések exportálása
Ez az útvonal lehetővé teszi, hogy bármilyen analitikai lekérdezést írjon, vagy exportálja a használati tölcséreket, majd exportálja azt egy Power BI irányítópultra. (Hozzáadhatja az adapter által létrehozott irányítópulthoz.)

### <a name="one-time-install-power-bi-desktop"></a>Egyszer: install Power BI Desktop
A Application Insights-lekérdezés importálásához használja a Power BI asztali verzióját. Ezt követően közzéteheti a weben vagy a Power BI felhőalapú munkaterületén. 

Telepítse a [Power bi Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Elemzési lekérdezés exportálása
1. [Nyissa meg az elemzést, és írja le a lekérdezést](../logs/log-analytics-tutorial.md).
2. Tesztelje és pontosítsa a lekérdezést, amíg nem elégedett az eredményekkel. Exportálás előtt győződjön meg arról, hogy a lekérdezés megfelelően fut az Analyticsben.
3. Az **Exportálás** menüben válassza a **Power bi (M)** lehetőséget. Mentse a szövegfájlt.
   
    ![Képernyőkép az elemzésről, az Exportálás menü kiemelve](./media/export-power-bi/analytics-export-power-bi.png)
4. A Power bi Desktop **területen válassza az**  >  **adatlekérdezés üres lekérdezés** lehetőséget. Ezután a lekérdezés szerkesztő **nézet** területén válassza a **speciális szerkesztő** lehetőséget.

    Illessze be az exportált M nyelvi szkriptet a Speciális szerkesztőba.

    ![Képernyőkép a Power BI Desktopről, Speciális szerkesztő kiemelve](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Előfordulhat, hogy a Power BI az Azure-hoz való hozzáférés engedélyezéséhez meg kell adnia a hitelesítő adatokat. A **szervezeti fiók** használatával jelentkezzen be Microsoft-fiók.
   
    ![A Power BI lekérdezési beállítások párbeszédpanel képernyőképe](./media/export-power-bi/power-bi-import-sign-in.png)

    Ha ellenőriznie kell a hitelesítő adatokat, használja a lekérdezés-szerkesztő **adatforrás-beállítások** menüjének utasításait. Ügyeljen arra, hogy megadják az Azure-hoz használt hitelesítő adatokat, amelyek eltérőek lehetnek a Power BI hitelesítő adataitól.
6. Válasszon vizualizációt a lekérdezéshez, és válassza ki az x tengely, az y tengely és a szegmentálás dimenzió mezőit.
   
    ![Képernyőkép a Power BI Desktop vizualizációs lehetőségeiről](./media/export-power-bi/power-bi-analytics-visualize.png)
7. A jelentés közzététele a Power BI felhőalapú munkaterületen. Innen egy szinkronizált verziót is beágyazhat más weblapokra.
   
    ![Képernyőkép a Power BI Desktopről, a közzététel gomb kiemelésével](./media/export-power-bi/publish-power-bi.png)
8. Frissítse a jelentést manuálisan időközönként, vagy állítson be ütemezett frissítést a beállítások lapon.

### <a name="export-a-funnel"></a>Tölcsér exportálása
1. [Készítse el a tölcsért](./usage-funnels.md).
2. Válassza a **Power bi** lehetőséget.

   ![Power BI gomb képernyőképe](./media/export-power-bi/button.png)

3. A Power bi Desktop **területen válassza az**  >  **adatlekérdezés üres lekérdezés** lehetőséget. Ezután a lekérdezés szerkesztő **nézet** területén válassza a **speciális szerkesztő** lehetőséget.

   ![Képernyőkép a Power BI Desktopről, az üres lekérdezés gomb kiemelve](./media/export-power-bi/blankquery.png)

   Illessze be az exportált M nyelvi szkriptet a Speciális szerkesztőba. 

   ![A képernyőképen látható a Power BI Desktop Speciális szerkesztő kiemelve](./media/export-power-bi/advancedquery.png)

4. Válassza ki az elemeket a lekérdezésből, és válassza ki a tölcsér vizualizációját.

   ![Képernyőfelvétel a Power BI Desktop tölcsér vizualizációs lehetőségeiről](./media/export-power-bi/selectsequence.png)

5. Módosítsa a címet, hogy az értelmes legyen, és tegye közzé a jelentést a Power BI felhőalapú munkaterületen. 

   ![Képernyőkép a Power BI Desktopről, a cím módosítása kiemelve](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Hibaelhárítás

A hitelesítő adatokkal vagy az adatkészlet méretétől kapcsolatos hibák merülhetnek fel. Itt talál néhány információt arról, hogy mi a teendő a hibákkal kapcsolatban.

### <a name="unauthorized-401-or-403"></a>Nem engedélyezett (401 vagy 403)
Ez akkor fordulhat elő, ha a frissítési jogkivonat nem frissült. Próbálja ki ezeket a lépéseket annak biztosításához, hogy továbbra is hozzáférjen:

1. Jelentkezzen be a Azure Portalba, és győződjön meg arról, hogy el tudja érni az erőforrást.
2. Próbálja meg frissíteni az irányítópult hitelesítő adatait.
3. Próbálja meg törölni a gyorsítótárat a Power BI Desktop.


   Ha rendelkezik hozzáféréssel, és a hitelesítő adatok frissítése nem működik, nyisson meg egy támogatási jegyet.

### <a name="bad-gateway-502"></a>Hibás átjáró (502)
Ezt általában egy olyan elemzési lekérdezés okozza, amely túl sok adattal tér vissza. Próbálkozzon kisebb időintervallum használatával a lekérdezéshez. 

Ha az elemzési lekérdezésből származó adatkészlet csökkentése nem felel meg a követelményeknek, érdemes lehet az [API](https://dev.applicationinsights.io/documentation/overview) -t használni egy nagyobb adatkészlet lekéréséhez. Az M-Query exportálásának átalakítása az API használatára.

1. Hozzon létre egy [API-kulcsot](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Frissítse az elemzésből exportált Power BI M parancsfájlt a Azure Resource Manager URL-cím a Application Insights API-val való lecserélésével.
   * **Https: \/ /Management.Azure.com/Subscriptions/cseréje..** .
   * , **https: \/ /API.applicationinsights.IO/Beta/apps/..** .
3. Végül frissítse a hitelesítő adatokat az alapszintű verzióra, és használja az API-kulcsot.

**Meglévő parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Frissített parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Mintavételezés
Az alkalmazás által küldött adatmennyiségtől függően érdemes lehet az adaptív mintavételezési funkciót használni, amely csak a telemetria egy adott százalékát küldi el. Ugyanez igaz, ha manuálisan állította be a mintavételezést az SDK-ban vagy a betöltéskor. [További információ a mintavételezésről](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI adapter (elavult)
Ez a metódus létrehoz egy teljes irányítópultot a telemetria. A kezdeti adatkészlet előre definiálva van, de további adatokat is hozzáadhat hozzá.

### <a name="get-the-adapter"></a>Az adapter beszerzése
1. Jelentkezzen be a [Power BI-ba](https://app.powerbi.com/).
2. Nyissa meg az **adatolvasás** ![ képernyőképet a GetData ikon bal alsó sarkában ](./media/export-power-bi/001.png) , a **szolgáltatások** területen.

    ![A Képernyőképek a szolgáltatások ablakban a beolvasás gombra mutatnak.](./media/export-power-bi/002.png)

3. Válassza a **Letöltés most** Application Insights alatt lehetőséget.

   ![A Application Insights adatforrásból beolvasott adatok képernyőképei](./media/export-power-bi/003.png)
4. Adja meg a Application Insights erőforrás adatait, majd **Jelentkezzen be**.

    ![Képernyőfelvétel: Kapcsolódás Application Insights ablakhoz.](./media/export-power-bi/005.png)

     Ezek az információk a Application Insights áttekintés ablaktáblán találhatók:

     ![Képernyőkép a beolvasásról Application Insights adatforrásról](./media/export-power-bi/004.png)

5. Nyissa meg az újonnan létrehozott Application Insights Power BI alkalmazást.

6. Várjon egy percet vagy kettőt az importálandó adatimportáláshoz.

    ![Képernyőkép Power BI adapterről](./media/export-power-bi/010.png)

Szerkesztheti az irányítópultot, kombinálhatja a Application Insights diagramokat más forrásokkal, valamint elemzési lekérdezésekkel. Több diagramot is megadhat a vizualizációs galériában, és mindegyik diagramhoz beállíthat paramétereket.

A kezdeti importálás után az irányítópult és a jelentések naponta frissülnek. A frissítési ütemtervet az adatkészleten is szabályozhatja.

## <a name="next-steps"></a>Következő lépések
* [Power BI – Learn](https://www.powerbi.com/learning/)
* [Elemzési oktatóanyag](../logs/log-analytics-tutorial.md)