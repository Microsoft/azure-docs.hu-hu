---
title: A vadászati könyvjelzők használata adatvizsgálatokhoz az Azure Sentinel szolgáltatásban
description: Ez a cikk azt ismerteti, hogyan használható az Azure Sentinel vadászati könyvjelzői az adatnyomon követéshez.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 5e3b6802bab2c5fae97be8e55c8d5c11ff570f78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94651312"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Adatok nyomon követése az Azure Sentinellel való veszélyforrás-keresés közben

A fenyegetések vadászata jellemzően a kártékony viselkedés bizonyítékait keresi a naplózott adatok hegyeinek áttekintéséhez. Ezen eljárás során a nyomozók a lehetséges hipotézisek érvényesítése és a kompromisszumok teljes történetének megismerése során megkeresik azokat az eseményeket, amelyeket érdemes megjegyezni, újra felkeresni és elemezni.

Az Azure Sentinelben elérhető vadászati könyvjelzők segítségével elvégezheti az **Azure Sentinel-logs-naplókban** futtatott lekérdezések megőrzését, valamint a szükségesnek ítélt lekérdezési eredményeket. Megjegyzések és címkék hozzáadásával emellett a környezettel kapcsolatos megfigyeléseit is feljegyezheti és lehivatkozhatja. A könyvjelzővel ellátott adatokat Ön és csapata is láthatja a könnyebb együttműködés érdekében.

A beérkező könyvjelzők adatait bármikor újra felkeresheti a **vadászat** ablaktábla **könyvjelzők** lapján. Szűrési és keresési lehetőségekkel gyorsan megkereshet adott, az aktuális vizsgálathoz szükséges adatokat. Azt is megteheti, hogy megtekinti a könyvjelzővel ellátott adatait közvetlenül a Log Analytics munkaterület **HuntingBookmark** táblájában. Például:

> [!div class="mx-imgBorder"]
> ![HuntingBookmark-tábla megtekintése](./media/bookmarks/bookmark-table.png)

A könyvjelzők táblázatból való megtekintése lehetővé teszi a könyvjelzővel ellátott adatok szűrését, összegzését és más adatforrásokkal való csatlakoztatását, így könnyen megkeresheti a egybehangzó-bizonyítékokat.

Jelenleg előzetes verzióban érhető el, ha olyan dolgot talál, amelyet sürgősen meg kell oldani a naplók vadászata közben, néhány kattintással létrehozhat egy könyvjelzőt, és előléptetheti azt egy incidensbe, vagy hozzáadhatja a könyvjelzőt egy meglévő incidenshez. További információ az incidensekről [: oktatóanyag: incidensek vizsgálata az Azure Sentinel szolgáltatással](tutorial-investigate-cases.md). 

Az előzetes verzióban is megjelenítheti a könyvjelzővel ellátott adatokat a könyvjelző részletei közül a **vizsgálat** gombra kattintva. Ez elindítja a vizsgálati folyamatot, amelyben megtekintheti, megvizsgálhatja és vizuálisan továbbíthatja az eredményeket egy interaktív entitás – gráf diagram és idősor használatával.

## <a name="add-a-bookmark"></a>Könyvjelző hozzáadása

1. A Azure Portalban navigáljon a **Sentinel**  >  **Threat Management**  >  **vadászathoz** , és futtasson lekérdezéseket a gyanús és rendellenes viselkedés érdekében.

2. Válassza ki az egyik vadászati lekérdezést, és a jobb oldalon, a vadászati lekérdezés részleteiben válassza a **lekérdezés futtatása** lehetőséget. 

3. Válassza a **lekérdezés eredményeinek megtekintése** lehetőséget. Például:
    
    > [!div class="mx-imgBorder"]
    > ![lekérdezési eredmények megtekintése az Azure Sentinel-vadászatból](./media/bookmarks/new-processes-observed-example.png)
    
    Ez a művelet megnyitja a lekérdezési eredményeket a **naplók** ablaktáblán.

4. A naplók lekérdezésének eredményei listán a jelölőnégyzetek használatával jelöljön ki egy vagy több olyan sort, amely az érdekesnek talált információkat tartalmazza.

5. Válassza a **Könyvjelző hozzáadása** lehetőséget:
    
    > [!div class="mx-imgBorder"]
    > ![Vadászati könyvjelző hozzáadása a lekérdezéshez](./media/bookmarks/add-hunting-bookmark.png)

6. A jobb oldalon a **Könyvjelző hozzáadása** panelen igény szerint frissítheti a könyvjelző nevét, címkékkel és megjegyzésekkel láthatja el, hogy milyen érdekes az elem.

7. A **lekérdezési adatok** szakaszban a legördülő lista segítségével kinyerheti az adatokat a **fiók**, a **gazdagép** és az **IP-cím** típusú entitások lekérdezési eredményeiből. Ez a művelet leképezi a kiválasztott entitás típusát egy adott oszlopra a lekérdezési eredményből. Például:
    
    > [!div class="mx-imgBorder"]
    > ![Entitások típusának hozzárendelése a vadászati könyvjelzőhöz](./media/bookmarks/map-entity-types-bookmark.png)
    
    Ha meg szeretné tekinteni a könyvjelzőt a vizsgálati gráfban (jelenleg előzetes verzióban), legalább egy olyan entitást le kell képeznie, amelyik a **fiók**, a **gazdagép** vagy az **IP-cím**. 

5. A módosítások elvégzéséhez és a könyvjelző hozzáadásához kattintson a **Mentés** gombra. Minden könyvjelzővel ellátott adattal megosztanak más nyomozókat, és ez az első lépés az együttműködési vizsgálat során.

 
> [!NOTE]
> A napló lekérdezési eredményei támogatják a könyvjelzőket, valahányszor megnyitják ezt a panelt az Azure Sentinelből. Kiválaszthatja például az **általános**  >  **naplók** elemet a navigációs sávon, kiválaszthatja az események hivatkozásait a vizsgálatok gráfban, vagy kijelölhet egy riasztási azonosítót az incidensek teljes részletei közül (jelenleg előzetes verzióban érhető el). Nem hozhat létre könyvjelzőket, ha a **naplók** panel más helyekről nyílik meg, például közvetlenül a Azure monitorból.

## <a name="view-and-update-bookmarks"></a>Könyvjelzők megtekintése és frissítése 

1. A Azure Portal navigáljon a **Sentinel**  >  **Threat Management**  >  **vadászathoz**. 

2. A könyvjelzők listájának megtekintéséhez kattintson a **könyvjelzők** fülre.

3. Ha segítségre van szüksége egy adott könyvjelző megkereséséhez, használja a keresőmező vagy a szűrő beállításait.

4. Válassza az egyéni könyvjelzők lehetőséget, és a jobb oldali részletek panelen tekintse meg a könyvjelző részleteit.

5. Szükség szerint végezze el a módosításokat, amelyek automatikusan mentve lesznek.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>A könyvjelzők feltárása a vizsgálati gráfban

> [!IMPORTANT]
> A vizsgálati gráfban található könyvjelzők feltárása és maga a vizsgálati gráf jelenleg nyilvános előzetes verzióban érhető el.
> Ezeket a szolgáltatásokat szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. A Azure Portal keresse meg a **Sentinel**  >  **Threat Management**  >  **vadászati**  >  **könyvjelzők** fület, és válassza ki a vizsgálni kívánt könyvjelzőt vagy könyvjelzőket.

2. A könyvjelző részletei területen győződjön meg arról, hogy legalább egy entitás le van képezve. Az **entitások** esetében például az **IP-cím**, a **gép** vagy a **fiók** bejegyzései jelennek meg.

3. Kattintson **a vizsgálat gombra a** könyvjelző a vizsgálati gráfban való megtekintéséhez.

A vizsgálati gráf használatára vonatkozó utasításokért tekintse meg a részletes elemzés [a vizsgálati gráf használatával](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)című témakört.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Könyvjelzők hozzáadása egy új vagy meglévő incidenshez

> [!IMPORTANT]
> A könyvjelzők új vagy meglévő incidenshez való hozzáadása jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. A Azure Portal navigáljon a **Sentinel**  >  **Threat Management**  >  **vadászati**  >  **könyvjelzők** lapra, és válassza ki az incidenshez hozzáadni kívánt könyvjelzőt vagy könyvjelzőket.

2. Válassza az **incidens műveletek (előzetes verzió)** lehetőséget a parancssáv:
    
    > [!div class="mx-imgBorder"]
    > ![Könyvjelzők felvétele az incidensbe](./media/bookmarks/incident-actions.png)

3. Szükség szerint válassza az **új incidens létrehozása** vagy **a meglévő incidenshez való hozzáadás** lehetőséget. Ezután:
    
    - Új incidens esetén: igény szerint frissítheti az incidens részleteit, majd válassza a **Létrehozás** lehetőséget.
    - Könyvjelzők meglévő incidenshez való hozzáadásához: válasszon ki egy incidenst, majd válassza a **Hozzáadás** lehetőséget. 

Ha meg szeretné tekinteni a könyvjelzőt az incidensen belül: navigáljon a **Sentinel**  >  **Threat Management**  >  **incidensekhez** , és válassza ki az incidenst a könyvjelzővel. Válassza a **teljes részletek megtekintése** lehetőséget, majd kattintson a **könyvjelzők** fülre.

> [!TIP]
> A parancssáv **incidensek (előzetes verzió)** lehetőségének alternatívájaként a menüsávon a helyi menü (**..**.) segítségével választhatja ki az **új incidens létrehozására**, **a meglévő incidenshez való hozzáadására** és **az incidensből való eltávolításra** vonatkozó beállításokat. 

## <a name="view-bookmarked-data-in-logs"></a>Könyvjelzővel ellátott adatnaplók megtekintése

A könyvjelzővel ellátott lekérdezések, eredmények vagy előzmények megtekintéséhez jelölje ki a könyvjelzőt a **vadászati**  >  **könyvjelzők** lapról, és használja a részleteket tartalmazó ablaktáblán található hivatkozásokat: 

- A forrás **lekérdezés megtekintése** a forrás lekérdezés megtekintéséhez a **naplók** ablaktáblán.

- A **könyvjelzők naplóiban** megtekintheti az összes könyvjelző-metaadatot, beleértve a frissítést, a frissített értékeket és a frissítés időpontját.

Az összes könyvjelzőhöz tartozó nyers könyvjelzők adatait úgy is megtekintheti, hogy kiválasztja a **könyvjelzőket** a **vadászati**  >  **könyvjelzők** lap menüsávján:

> [!div class="mx-imgBorder"]
> ![Könyvjelző-naplók](./media/bookmarks/bookmark-logs.png)

Ez a nézet megjeleníti a társított metaadatokkal rendelkező összes könyvjelzőt. A [Kulcsszóválasztó lekérdezési nyelv](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) lekérdezésekkel leszűrheti a keresett könyvjelző legújabb verzióját.

> [!NOTE]
> A könyvjelzők létrehozása és a **könyvjelzők** lapon megjelenő idő között jelentős késleltetés (percben mérve) lehet.

## <a name="delete-a-bookmark"></a>Könyvjelző törlése
 
1.  A Azure Portal navigáljon a **Sentinel**  >  **Threat Management**  >  **vadászati**  >  **könyvjelzők** lapra, és válassza ki a törölni kívánt könyvjelzőt vagy könyvjelzőket. 

2. Kattintson a jobb gombbal a kiválasztott elemekre, és válassza a könyvjelző vagy könyvjelzők törlésének lehetőségét. Ha például csak egy könyvjelzőt jelölt ki, törölje a **könyvjelzőt** , és ha két könyvjelzőt jelölt ki, **törölheti a 2** könyvjelzőt.
    
A könyvjelző törlése eltávolítja a könyvjelzőt a **Könyvjelző** lapon lévő listából. A Log Analytics munkaterület **HuntingBookmark** táblázata továbbra is az előző könyvjelző-bejegyzéseket fogja tartalmazni, a legújabb bejegyzés azonban igaz értékre módosítja a **SoftDelete** értéket, így a régi könyvjelzők kiszűrhetők. A könyvjelzők törlése nem távolítja el a más könyvjelzőhöz vagy riasztásokhoz társított vizsgálati élményből származó entitásokat. 


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan futtathat egy vadászati vizsgálatot a könyvjelzők használatával az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:


- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [Automatikus vadászati kampányok futtatása jegyzetfüzetek használatával](notebooks.md)