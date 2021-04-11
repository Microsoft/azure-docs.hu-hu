---
title: Naplók és metrikák megjelenítése Power BI
description: Az Azure Cognitive Search-naplók és-metrikák megjelenítése Power BIokkal.
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: fbbeb861e50abfd393b416ddc46ff147fffb7b8e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581626"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Azure Cognitive Search-naplók és-metrikák megjelenítése Power BI

Az [azure Cognitive Search](./search-what-is-azure-search.md) elküldhetik a műveleti naplókat és a szolgáltatási metrikákat egy Azure Storage-fiókba, amelyet aztán Power bi jeleníthet meg. Ez a cikk ismerteti a lépéseket, valamint azt, hogy hogyan jeleníthető meg az Power BI sablon alkalmazása az adatmegjelenítéshez. A sablon segítségével részletes információkhoz juthat a keresési szolgáltatásról, beleértve a lekérdezésekkel, az indexeléssel, a műveletekkel és a szolgáltatási metrikákkal kapcsolatos információkat.

A Power BI template app **Azure Cognitive Search: a naplók és a metrikák elemzése** a [Power bi apps piactéren](https://appsource.microsoft.com/marketplace/apps).

## <a name="set-up-the-app"></a>Az alkalmazás beállítása

1. A keresési szolgáltatás metrikájának és erőforrás-naplózásának engedélyezése:

    1. Hozzon létre vagy azonosítson egy meglévő [Azure Storage-fiókot](../storage/common/storage-account-create.md) , ahol archiválhatja a naplókat
    1. Navigáljon az Azure Cognitive Search szolgáltatásra a Azure Portal
    1. A bal oldali oszlopban a figyelés szakaszban válassza a **diagnosztikai beállítások** elemet.

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="A diagnosztikai beállítások kiválasztását bemutató képernyőkép az Azure Cognitive Search szolgáltatás figyelés szakaszában." border="false":::

    1. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget
    1. Keresse meg az **archívumot egy Storage-fiókban**, adja meg a Storage-fiók adatait, és keresse meg a **OperationLogs** és a **AllMetrics**

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Képernyőfelvétel a mérőszámok és az erőforrás-naplózás kiválasztásáról a diagnosztikai beállítások lapon.":::
    1. Válassza a **Mentés** lehetőséget

1. A naplózás engedélyezése után a keresési szolgáltatással megkezdheti a naplók és mérőszámok generálását. Akár egy óráig is eltarthat, amíg a tárolók megjelennek a blob Storage-ban ezekkel a naplókkal. Ekkor megjelenik egy elemzések – **naplók – operationlogs** tároló a forgalmi naplók és az elemzések – **mérőszámok – pt1m** tároló a metrikák számára.

1. Keresse meg az Azure Cognitive Search Power BI alkalmazást a [Power bi alkalmazások piactéren](https://appsource.microsoft.com/marketplace/apps) , és telepítse egy új munkaterületre vagy egy meglévő munkaterületre. Az alkalmazás neve **Azure Cognitive Search: a naplók és a metrikák elemzése**.

1. Az alkalmazás telepítése után válassza ki az alkalmazást az alkalmazások listájából Power BI.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Képernyőfelvétel: az Azure Cognitive Search alkalmazás, amely az alkalmazások listájából választható ki.":::

1. Válassza a **Kapcsolódás** lehetőséget az adatkapcsolathoz

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Az Azure Cognitive Search alkalmazásban az adataihoz való kapcsolódást bemutató képernyőkép.":::

1. Adja meg a naplókat és mérőszámokat tartalmazó Storage-fiók nevét. Alapértelmezés szerint az alkalmazás az utolsó 10 napot fogja megtekinteni, de ezt az értéket a **Days** paraméterrel lehet megváltoztatni.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Képernyőfelvétel: a Storage-fiók nevének és a lekérdezési napok számának beírása a Kapcsolódás az Azure-Cognitive Search lapra.":::

1. A hitelesítési módszerként válassza a **kulcs** lehetőséget, és adja meg a Storage-fiók kulcsát. Adatvédelmi szintként válassza a **magánjellegű** lehetőséget. Kattintson a Bejelentkezés gombra. Ennek hatására elkezdődik a betöltés.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Képernyőfelvétel: a hitelesítési módszer, a fiók kulcsa és az adatvédelmi szint bevitele a Kapcsolódás az Azure-Cognitive Search lapra.":::

1. Várjon, amíg az adatgyűjtés frissül. Ez eltarthat egy ideig, attól függően, hogy mennyi adattal rendelkezik. Láthatja, hogy az alábbi kijelző alapján a rendszer továbbra is frissíti-e az adott információt.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Az adatfrissítési lapon található információk olvasását bemutató képernyőkép.":::

1. Az Adatfrissítés befejezését követően válassza az **Azure Cognitive Search jelentés** lehetőséget a jelentés megtekintéséhez.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Az Azure Cognitive Search jelentés az Adatfrissítés lapon való kiválasztását bemutató képernyőkép.":::

1. Győződjön meg arról, hogy a jelentés megnyitása után frissítse az oldalt, hogy az megnyíljon az adataival.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Képernyőkép az Azure Cognitive Search Power BI jelentésről.":::

## <a name="modify-app-parameters"></a>Alkalmazás paramétereinek módosítása

Ha egy másik Storage-fiókból kívánja megjeleníteni az adatok megjelenítését, vagy módosítani szeretné a lekérdezési napok számát, kövesse az alábbi lépéseket a **napok** és a **StorageAccount** paramétereinek módosításához.

1. Navigáljon a Power BI alkalmazásaihoz, keresse meg az Azure Cognitive Search alkalmazást, és válassza az **alkalmazás szerkesztése** gombot a munkaterület megtekintéséhez.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Az Azure Cognitive Search-alkalmazás alkalmazás szerkesztése gombjának kijelölését bemutató képernyőkép.":::

1. Válassza a **Beállítások** lehetőséget az adatkészlet beállításai közül.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Képernyőfelvétel: beállítások kiválasztása az Azure Cognitive Search adatkészlet beállításai között.":::

1. Az adatkészletek lapon módosítsa a paraméterek értékeit, majd válassza az **alkalmaz** lehetőséget. Ha probléma merül fel a kapcsolatban, frissítse az adatforrás hitelesítő adatait ugyanazon a lapon.

1. Lépjen vissza a munkaterületre, és válassza a **Frissítés most** lehetőséget az adatkészlet beállításai között.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Képernyőfelvétel: a frissítés most lehetőség kiválasztására az Azure Cognitive Search adatkészlet beállításai között.":::

1. Nyissa meg a jelentést a frissített adatértékek megtekintéséhez. Előfordulhat, hogy frissítenie kell a jelentést a legfrissebb adatértékek megtekintéséhez.

## <a name="troubleshooting-report-issues"></a>Jelentésekkel kapcsolatos problémák elhárítása

Ha úgy találja, hogy az adatai nem láthatók, kövesse az alábbi hibaelhárítási lépéseket:

1. Nyissa meg a jelentést, és frissítse az oldalt, és győződjön meg róla, hogy a legfrissebb adatnézeteket tekinti meg. A jelentésben lehetőség van az adatfrissítésre. Ezzel a beállítással hozzájuthat a legfrissebb értékekhez.

1. Győződjön meg arról, hogy a Storage-fiók neve és a megadott hozzáférési kulcs helyes. A Storage-fiók nevének meg kell egyeznie a keresési szolgáltatás naplóival konfigurált fiók nevével.

1. Győződjön meg arról, hogy a Storage-fiók tartalmazza a tárolók elemzéseit – **naplók – operationlogs** és megállapítások **– mérőszámok – pt1m** és minden tároló rendelkezik adatokkal. A naplók és a metrikák a mappák pár rétegén belül lesznek.

1. Ellenőrizze, hogy az adatkészlet továbbra is frissül-e. A frissítési állapot jelzője a fenti 8. lépésben látható. Ha továbbra is frissül, várjon, amíg a frissítés be nem fejeződik a jelentés megnyitásához és frissítéséhez.

## <a name="next-steps"></a>Következő lépések

+ [Keresési műveletek és tevékenységek figyelése](search-monitor-usage.md)
+ [Mi az a Power BI?](/power-bi/fundamentals/power-bi-overview)
+ [A Power BI szolgáltatás alapfogalmai tervezők számára](/power-bi/service-basic-concepts)