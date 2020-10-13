---
title: 'Oktatóanyag: figyelés és Finomhangolás – Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Ez az oktatóanyag végigvezeti a figyelésen és hangoláson Azure Database for PostgreSQL-egyetlen kiszolgálón.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: f211ce7e52ccaa6793435159d0ce1c64fe8b63ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91705281"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Oktatóanyag: Azure Database for PostgreSQL figyelése és finomhangolása – egyetlen kiszolgáló

Az Azure Database for PostgreSQL olyan funkciókkal rendelkezik, amelyek segítenek megérteni és javítani a kiszolgálója teljesítményét. Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Lekérdezés engedélyezése és várakozási statisztikák összegyűjtése
> * Az összegyűjtött adatok elérése és felhasználása
> * A lekérdezések teljesítményének és a várakozási statisztikák időbeli alakulásának megtekintése
> * Adatbázis-elemzés a teljesítménnyel kapcsolatos javaslatokért
> * Teljesítménnyel kapcsolatos javaslatok alkalmazása

## <a name="before-you-begin"></a>Előkészületek
Szükség van egy 9.6-os vagy 10-es verziójú PostgreSQL-t futtató Azure Database for PostgreSQL kiszolgálóra. A kiszolgáló létrehozásához kövesse a [Létrehozási oktatóanyagban](tutorial-design-database-using-azure-portal.md) megadott lépéseket.

> [!IMPORTANT]
> A **Lekérdezéstár**, a **Lekérdezési terheléselemző** és a **Teljesítménnyel kapcsolatos javaslatok** nyilvános előzetes verzióban érhetők el.

## <a name="enabling-data-collection"></a>Adatgyűjtés engedélyezése
A [Lekérdezéstár](concepts-query-store.md) rögzíti a kiszolgálója lekérdezéseinek és várakozási statisztikáinak előzményeit, és a kiszolgáló **azure_sys** adatbázisában tárolja őket. Ez egy bekapcsolható funkció. Az engedélyezéséhez:

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Jelölje ki a menü bal oldalon lévő **Beállítások** részében található **kiszolgálóparamétereket**.

4. A lekérdezési teljesítményadatok gyűjtésének megkezdéséhez a **pg_qs.query_capture_mode** paramétert állítsa **TOP** értékre. A várakozási statisztikák gyűjtésének megkezdéséhez a **pgms_wait_sampling.query_capture_mode** paramétert állítsa **ALL** értékre. Mentse el.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="A Lekérdezéstár kiszolgálóparaméterei":::

5. Engedélyezze az első köteg adat 20 percig történő tárolását az **azure_sys** adatbázisban.


## <a name="performance-insights"></a>Teljesítményelemzések
A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg. 

1. Az Azure Database for PostgreSQL kiszolgálója portáloldalán, a menü bal oldalon lévő **Támogatás és hibaelhárítás** része alatt válassza a **Lekérdezési terheléselemző** funkciót.

2. A **Hosszú ideig futó lekérdezések** lapon az 5 leggyakoribb lekérdezés látható a végrehajtásonkénti átlagos időtartam szerint, 15 perces időközönként összesítve. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="A Lekérdezéstár kiszolgálóparaméterei":::

   Több lekérdezés megtekintéséhez válasszon értéket a **Lekérdezések száma** legördülő menüből. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

3. Kattintással és húzással egy adott időablakra szűkítheti a diagramot.

4. Kisebb vagy nagyobb időtartam megtekintéséhez használja a nagyítás és a kicsinyítés ikont.

5. Az adott időablak hosszú ideig futó lekérdezéseivel kapcsolatos további részletekhez tekintse meg a diagram alatti táblázatot.

6. A kiszolgálóval kapcsolatos megfelelő várakozási adatok vizualizációinak megjelenítéséhez válassza a **Várakozási statisztikák** fület.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="A Lekérdezéstár kiszolgálóparaméterei":::

### <a name="permissions"></a>Engedélyek
A Lekérdezési terheléselemző lekérdezéseinek szövegét **Tulajdonos** vagy **Közreműködő** jogosultsággal lehet megjeleníteni. **Olvasó** jogosultsággal a diagramok és táblázatok megtekinthetők, de a lekérdezés szövege nem.


## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

1. Nyissa meg a PostgreSQL-kiszolgálója Azure portáloldalán lévő menüsáv **Támogatás és hibaelhárítás** részén lévő **Teljesítménnyel kapcsolatos javaslatok** menüpontot.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="A Lekérdezéstár kiszolgálóparaméterei":::

2. Jelölje ki az **Elemzés** funkciót, és válasszon egy adatbázist. Ez elindítja az elemzést.

3. A számítási feladatoktól függően a folyamat több percet is igénybe vehet. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés.

4. A **Teljesítménnyel kapcsolatos javaslatok** ablakban megjelenik a javaslatok listája (ha vannak). 

5. A javaslat információt jelenít meg a releváns **adatbázisról**, **tábláról**, **oszlopról** és **indexméretről**.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="A Lekérdezéstár kiszolgálóparaméterei":::

6. A javaslat megvalósításához másolja ki a lekérdezés szövegét, és futtassa azt a választott ügyfélről.

### <a name="permissions"></a>Engedélyek
A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).