---
title: Azure-Log Analytics-beli adatimportálás az Power BIba | Microsoft Docs
description: A Power BI egy felhőalapú üzleti elemzési szolgáltatás a Microsofttól, amely sokoldalú vizualizációkat és jelentéseket nyújt a különböző adatkészletek elemzéséhez.  Ez a cikk ismerteti, hogyan konfigurálhatja és importálhatja Log Analyticsi az információkat a Power BIba, és hogyan konfigurálhatja az automatikus frissítéshez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: b562235c9cb644940b9018a950faa5ad82a665dc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616125"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Azure Monitor naplózási adatnaplóba való importálás Power BI


A [Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) egy felhőalapú üzleti elemzési szolgáltatás a Microsofttól, amely sokoldalú vizualizációkat és jelentéseket nyújt a különböző adatkészletek elemzéséhez.  Egy Azure Monitor-naplózási lekérdezés eredményét egy Power BI adatkészletbe importálhatja, így kihasználhatja annak funkcióit, például a különböző forrásokból származó adatok egyesítését és a jelentések megosztását a webes és a mobileszközök esetében is.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Áttekintés
Ha Azure Monitor [log Analytics munkaterületről](../logs/manage-access.md) szeretne adatokat importálni a Power bi-ben, akkor az adatkészletet Power bi Azure monitor-beli [naplózási lekérdezés](../logs/log-query-overview.md) alapján hozza létre.  A lekérdezés minden alkalommal fut, amikor az adatkészlet frissül.  Ezután létrehozhat olyan Power BI jelentéseket, amelyek az adatkészletből származó adatokat használnak.  Az adatkészlet Power BI-ben való létrehozásához exportálja a lekérdezést Log Analyticsról [Power Query (M) nyelvre](/powerquery-m/power-query-m-language-specification).  Ezt követően létrehozhat egy lekérdezést a Power BI Desktopban, majd közzéteheti azt Power BI adatkészletként.  A folyamat részletei az alábbiakban olvashatók.

![Log Analytics Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Lekérdezés exportálása
Először hozzon létre egy olyan [log-lekérdezést](../logs/log-query-overview.md) , amely visszaadja azokat az adatokat, amelyeket fel szeretne tölteni a Power bi adatkészletből.  Ezután exportálja a lekérdezést [Power Query (M) nyelvre](/powerquery-m/power-query-m-language-specification) , amelyet a Power bi Desktop használhat.

> [!WARNING]
> Ügyeljen arra, hogy [optimalizálja a lekérdezést](../logs/query-optimization.md) úgy, hogy az ne tartson túl hosszú ideig, vagy időtúllépést okoz. Jegyezze fel az exportált lekérdezés **TimeSpan** értékét, amely meghatározza a lekérdezés által lekért TimeSpan. A lekérdezés által visszaadott adatok mennyiségének korlátozásához a legkisebb TimeSpan kell használnia.

1. Az adatkészlethez tartozó adatok kinyeréséhez [hozza létre a log Analytics napló lekérdezést](../logs/log-analytics-tutorial.md) .
2. Válassza az **Exportálás**  >  **Power bi lekérdezés (M)** lehetőséget.  Ezzel exportálja a lekérdezést egy **PowerBIQuery.txt** nevű szövegfájlba. 

    ![Naplóbeli keresés exportálása](media/powerbi/export-analytics.png)

3. Nyissa meg a szövegfájlt, és másolja a tartalmát.

## <a name="import-query-into-power-bi-desktop"></a>Lekérdezés importálása Power BI Desktopba
A Power BI Desktop egy asztali alkalmazás, amely lehetővé teszi, hogy a Power BI közzétett adatkészleteket és jelentéseket hozzon létre.  Azt is megteheti, hogy a Azure Monitorból exportált Power Query nyelvet használó lekérdezést hoz létre. 

1. Ha még nincs telepítve, telepítse a [Power bi Desktop](https://powerbi.microsoft.com/desktop/) , majd nyissa meg az alkalmazást.
2.   >  Új lekérdezés megnyitásához válassza az **adatlekérdezés üres lekérdezés** lehetőséget.  Ezután válassza ki **speciális szerkesztő** és illessze be az exportált fájl tartalmát a lekérdezésbe. Kattintson a **Kész** gombra.

    ![Power BI Desktop lekérdezés](media/powerbi/desktop-new-query.png)

5. A lekérdezés lefut, és megjeleníti az eredményeit.  Előfordulhat, hogy a rendszer a hitelesítő adatok megadását kéri az Azure-hoz való csatlakozáshoz.  
6. Adjon meg egy leíró nevet a lekérdezéshez.  Az alapértelmezett érték a **Query1**. Kattintson a **Bezárás gombra, és az alkalmazással** adja hozzá az adatkészletet a jelentéshez.

    ![Power BI Desktop neve](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Közzététel a Power BI-ban
Amikor közzéteszi a Power BI, egy adatkészletet és egy jelentést fog létrehozni.  Ha Power BI Desktop-ban hoz létre jelentést, akkor a rendszer közzéteszi az adatait.  Ha nem, akkor a rendszer üres jelentést hoz létre.  A jelentést Power BI módosíthatja, vagy létrehozhat egy újat az adatkészlet alapján.

1. Hozzon létre egy jelentést az adatai alapján.  Ha még nem ismeri, használja [Power bi Desktop dokumentációt](/power-bi/desktop-report-view) .  
1. Ha készen áll arra, hogy elküldje Power BIre, kattintson a **Közzététel** gombra.  
1. Ha a rendszer kéri, válasszon egy célhelyet a Power BI-fiókjában.  Ha nem rendelkezik konkrét céllal, használja a **saját munkaterületet**.

    ![Közzététel Power BI Desktop](media/powerbi/desktop-publish.png)

1. A közzététel befejezésekor kattintson a **megnyitás Power bi** lehetőségre a Power bi új adatkészlettel való megnyitásához.


### <a name="configure-scheduled-refresh"></a>Ütemezett frissítés beállítása
A Power BIban létrehozott adatkészlet ugyanazokat az adatokat fogja tartalmazni, amelyeket korábban a Power BI Desktop látott.  A lekérdezés újbóli futtatásához frissítenie kell az adatkészletet, és fel kell töltenie a Azure Monitor legújabb adataival.  

1. Kattintson arra a munkaterületre, ahol feltöltötte a jelentést, és válassza az **adatkészletek** menüt. 
1. Válassza ki a helyi menüt az új adatkészlet mellett, és válassza a **Beállítások** lehetőséget. 
1. Az **adatforrás hitelesítő** adatai területen a hitelesítő adatok érvénytelenek.  Ennek az az oka, hogy még nem adott meg hitelesítő adatokat az adatkészlet számára az adatok frissítésekor.  
1. Kattintson a **hitelesítő adatok szerkesztése** lehetőségre, és a Azure monitor log Analytics munkaterülethez hozzáféréssel rendelkező hitelesítő adatok megadásához. Ha kétfaktoros hitelesítést igényel, válassza a **OAuth2** lehetőséget a **hitelesítési módszer** megadásához, hogy a rendszer kérje a hitelesítő adatokkal való bejelentkezést.

    ![Power BI ütemterv](media/powerbi/powerbi-schedule.png)

5. Az **ütemezett frissítés** területen kapcsolja be az adatok naprakészen **tartásának** lehetőségét.  Igény szerint módosíthatja a **frissítési gyakoriságot** , és egy vagy több megadott időpontot a frissítés futtatásához.

    ![Power BI frissítés](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Következő lépések
* További információ a [naplók kereséséről](../logs/log-query-overview.md) a Power bi exportálható lekérdezések létrehozásához.
* További információ a [Power BIről](https://powerbi.microsoft.com) a vizualizációk Azure monitor naplók alapján történő létrehozásához.