---
title: A környezet összekötése a Power BI-Azure Time Series Insightsrel | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Time Series Insights a Power BIhoz a szervezeten belüli adatmegosztáshoz, diagramhoz és megjelenítéshez.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374167"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Adatok megjelenítése Azure Time Series Insightsról Power BI

A Azure Time Series Insights egy platform az idősoros adatsorozatok felhőben történő tárolásához, kezeléséhez, lekérdezéséhez és megjelenítéséhez. A [Power bi](https://powerbi.microsoft.com) egy üzleti elemzési eszköz, amely sokoldalú vizualizációs képességekkel rendelkezik, amelyekkel megoszthatja az elemzéseket és az eredményeket a szervezeten belül. Mindkét szolgáltatás integrálható, amely lehetővé teszi, hogy kibővítse a Azure Time Series Insights hatékony elemzését a Power BI erős adatvizualizációval és egyszerű megosztási képességeivel.

A következőket fogja megtanulni:

* Azure Time Series Insights összekapcsolása Power BI a natív Azure Time Series Insights-összekötő használatával
* Vizualizációk létrehozása az idősoros adataival Power BI
* A jelentés közzététele Power BI és megosztás a szervezet többi részével


## <a name="prerequisites"></a>Előfeltételek

* Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/) .
* Töltse le és telepítse a [Power bi Desktop](https://powerbi.microsoft.com/downloads/) legújabb verzióját
* [Azure Time Series Insights Gen2-környezet](./how-to-provision-manage.md) létrehozása vagy létrehozása

Tekintse át a [környezeti hozzáférési szabályzatokat](./concepts-access-policies.md) , és győződjön meg arról, hogy közvetlen hozzáféréssel vagy vendég hozzáféréssel rendelkezik a Azure Time Series Insights Gen2-környezethez. 

> [!IMPORTANT]
> * Töltse le és telepítse a [Power bi Desktop](https://powerbi.microsoft.com/downloads/)legújabb verzióját. Ha követni szeretné a cikkben ismertetett lépéseket, győződjön meg arról, hogy a Power BI Desktop telepítve van legalább a december 2020 (2.88.321.0) verziója. 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Adatok exportálása Azure Time Series Insightsból Power BI asztalra

Első lépések:

1. Nyissa meg az Azure Time Series Insights Gen2 Explorer alkalmazást, és adja meg az adatait. Az Power BIba exportálandó adatértékek.
1. Miután létrehozta a megfelelő nézetet, navigáljon a **További műveletek** legördülő menüre, és válassza a **Kapcsolódás Power bi** lehetőséget.

    [![Azure Time Series Insights Gen2 Explorer-exportálás](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Állítsa be a paramétereket az exportáláshoz:

   * **Adatformátum**: válassza ki, hogy szeretné-e exportálni az **összesített adatokat** vagy a **nyers eseményeket** a Power BIba. 

       > [!NOTE]
       > Ha nyers eseményeket exportál, akkor a Power BI később összesítheti ezeket az adatokat. Ha azonban összesített adatokat exportál, nem lehet visszaállítani a nyers adatokat Power BI. A nyers események szintjének maximális száma 250 000.

   * **Időtartomány**: válassza ki, hogy szeretné-e megtekinteni a **rögzített** időtartományt vagy a **legfrissebb** adatPower bi. A rögzített időtartomány kiválasztása azt jelenti, hogy a diagramon megadott keresési hatókörben lévő összes érték Power BI lesz exportálva. A legújabb időtartomány kiválasztása azt jelenti, hogy a Power BI fogja megragadni a kiválasztott keresési tartomány legfrissebb értékeit (például ha 1 órányi adatdiagramot jelöl ki, és a "legutóbbi" beállítást választja, a Power BI-összekötő mindig lekérdezéseket hajt végre a legújabb 1 órás adatmennyiséggel.)
  
   * **Áruház típusa**: válassza ki, hogy szeretné-e futtatni a kiválasztott lekérdezést a **meleg tárolón** vagy a **hűtőházi tárolón**. Ha olyan tartományt jelölt ki, amely a hideg és a meleg áruházakra is kiterjed, a lekérdezés alapértelmezés szerint a hűtőházi tárolóba lesz átirányítva, mivel a meleg tár csak a legfrissebb adatait fogja tartalmazni. A storeType paraméter manuális módosítása engedélyezett, de a legjobb élmény érdekében nem ajánlott. 

    > [!TIP] 
    > Azure Time Series Insights Explorer automatikusan kiválasztja az ajánlott paramétereket a keresési időtartamtól és az exportálásra kiválasztott adatok nézettől függően. 

1. Miután konfigurálta a beállításokat, válassza a **lekérdezés másolása a vágólapra** lehetőséget.

    [![Azure Time Series Insights Explorer exportálási modális](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Indítsa el a Power BI Desktopot.
   
1. A **kezdőlapon** Power bi Desktop kattintson a bal felső sarokban található **adatlekérdezés** elemre, majd a **tovább** gombra.

    [![Adatlekérdezés a Power BI-ban](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. Keressen **Azure Time Series Insights**, válassza a **Azure Time Series Insights (bétaverzió)**, majd a **kapcsolat** lehetőséget.

    [![Power BI összekötése a Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Másik lehetőségként navigáljon az **Azure** lapra, válassza a **Azure Time Series Insights (bétaverzió)**, majd a **Kapcsolódás** lehetőséget.

1. Illessze be a Azure Time Series Insights Intézőből másolt lekérdezést az **egyéni lekérdezési** mezőbe, majd nyomja meg az **OK** gombot.

    [![Illessze be az egyéni lekérdezést, és kattintson az OK gombra.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  Az adattábla ekkor betöltődik. A betöltéshez nyomja meg a **Load** (betöltés) Power bi Ha az adatátalakítást szeretné elvégezni, az **adatátalakítás** lehetőségre kattintva megteheti. Az adatait a betöltését követően is átalakíthatja.

    [![Tekintse át a táblázatban szereplő, és válassza a betöltés lehetőséget.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Vizualizációkat tartalmazó jelentés létrehozása

Most, hogy importálta az adatPower BIba, ideje, hogy készítsen egy jelentést a vizualizációkkal.

1. Győződjön meg arról, hogy az ablak bal oldalán a **jelentés** nézet van kiválasztva.

    [![Képernyőfelvétel: a jelentés nézet ikonja.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. A vizualizációk oszlopban válassza ki a kívánt **vizualizációt** . Válassza például a **vonal diagram** elemet. Ez egy üres vonalas diagramot ad hozzá a vászonhoz.

1.  A **mezők** listában válassza a **_Timestamp** lehetőséget, majd húzza a **tengely** mezőre az X tengely mentén látható idő megjelenítéséhez. Ügyeljen arra, hogy **_Timestampre** váltson a **tengely** értékeként (az alapértelmezett dátum- **hierarchia**).

    [![_Timestamp kiválasztása](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  A **mezők** listában válassza ki a ábrázolni kívánt változót, és húzza az **értékek mezőre az Y** tengely alatti értékek megjelenítéséhez. Válassza ki az idősorozat-azonosító értékét, és húzza a **Jelmagyarázat** mezőjére, hogy több sort hozzon létre a diagramon, egy idősorozat-azonosítóval. Ez egy, a Azure Time Series Insights Explorer által biztosított nézethez hasonlóan jelenik meg. 

    [![Alapszintű vonalas diagram létrehozása](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Ha egy másik diagramot szeretne hozzáadni a vászonhoz, válassza a vászonon bárhol a vásznon, és ismételje meg a folyamatot.

    [![További megosztható diagramok létrehozása](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Miután létrehozta a jelentést, közzéteheti Power BI Reporting Services szolgáltatásban, és megoszthatja másokkal a szervezetében.

## <a name="advanced-editing"></a>Speciális szerkesztés
Ha már betöltött egy adatkészletet a Power BIban, de módosítani szeretné a lekérdezést (például a dátum/idő vagy a környezeti azonosító paramétereit), ezt a Power BI Speciális szerkesztő funkcióján keresztül teheti meg. A [Power bi dokumentációjában](/power-bi/desktop-query-overview) tájékozódhat arról, hogyan végezheti el a módosításokat a **Power Query szerkesztő** használatával. 

## <a name="next-steps"></a>Következő lépések

* További információ a [Power bi asztalról](/power-bi/desktop-query-overview).

* További információ az Azure Time Series Insights Gen2 lévő [adatlekérdezésekről](concepts-query-overview.md) .