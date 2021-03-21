---
title: Egyéni paraméterekkel rendelkező munkafüzetek Azure Monitor
description: Összetett jelentéskészítés egyszerűsítése előre elkészített és egyéni paraméterekkel rendelkező munkafüzetekből
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723010"
---
# <a name="interactive-workbooks"></a>Interaktív munkafüzetek

A munkafüzetek lehetővé teszik, hogy a szerzők interaktív jelentéseket és tapasztalatokat hozzanak létre a felhasználók számára. Az interaktivitás több módon is támogatott.

## <a name="parameter-changes"></a>Paraméterek módosítása

Amikor egy munkafüzet felhasználó frissíti a paramétert, a paramétert használó vezérlők automatikusan frissítik és újrarajzolják az új állapotot. A Azure Portal-jelentések többsége támogatja az interaktivitást. A munkafüzetek egyszerű továbbítási módon biztosítják ezt a minimális felhasználói beavatkozást.

További információ a [munkafüzetek paramétereinek](workbooks-parameters.md) használatáról

## <a name="grid-tile-chart-selections"></a>Rács, csempe, diagram kiválasztása

A munkafüzetek lehetővé teszik, hogy a szerzők olyan forgatókönyveket hozzanak létre, amelyekben egy sor egy sorára kattintva frissíti az ezt követő diagramokat a sor tartalma alapján.

A felhasználók rendelkezhetnek például egy olyan rácstal, amely megjeleníti a kérelmek listáját, és néhány statisztikát, például a hibák számát. Úgy állíthatják be, hogy a kérelemnek megfelelő sorokra kattintva a rendszer a frissítés után részletes diagramokat hoz létre, hogy csak erre a kérésre szűrje.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Az interaktivitás beállítása a rácsvonalak sorában kattintson a

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A _lekérdezés hozzáadása_ hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát _naplóként_, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` az eredmények megtekintése
6. Válassza a _Speciális beállítások_ ikont a lekérdezés láblécében (az ikon úgy néz ki, mint egy fogaskerék). Ekkor megnyílik a speciális beállítások ablaktábla.
7. A beállítás bejelölése: `When an item is selected, export a parameter` .
8. Az Ön által bejelölt beállítás alatt válassza a *paraméter hozzáadása* lehetőséget, és töltse ki az alábbi információk alapján.
    1. Exportálandó mező: `Request`
    2. Paraméter neve: `SelectedRequest`
    3. Alapértelmezett érték: `All requests`
9. Mentés kiválasztása

    ![Képernyőfelvétel: a speciális szerkesztő, amely a mezők paraméterekként való exportálásának beállításait tartalmazza.](./media/workbooks-interactive/export-parameters-add.png)

10. Válassza a(z) `Done Editing` lehetőséget.
11. Adjon hozzá egy másik lekérdezés-vezérlőelemet a 2. és a 3. lépéssel.
12. A lekérdezési szerkesztővel adja meg az elemzéshez használandó KQL.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` az eredmények megtekintéséhez.
14. _Vizualizáció_ módosítása a következőre: `Area chart` .
15. Válassza ki az első rácson kijelölni kívánt sort. Figyelje meg, hogy az alábbi diagramterület a kiválasztott kérelemre szűri a szűrőket.

Az eredményül kapott jelentés szerkesztési módban így néz ki:

![Képernyőfelvétel: az első két lekérdezés szerkesztési módban.](./media/workbooks-interactive//interactivity-grid-create.png)

Az alábbi ábrán egy részletesebb interaktív jelentés olvasható az olvasási módban, ugyanazon alapelvek alapján. A jelentés rácsos kattintásokat használ a paraméterek exportálására – ez pedig két diagramon és egy szöveges blokkon is használatos.

![Egy jelentés képernyőképe olvasási módban a rácsos kattintások használatával.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Teljes sor tartalmának exportálása

Előfordulhat, hogy csak egy adott oszlop helyett érdemes exportálni a kijelölt sor teljes tartalmát. Ilyen esetekben ne törölje a `Field to export` tulajdonságot a fenti 7,1. lépésben. A munkafüzetek a teljes sor tartalmát a paraméternek megfelelő JSON-fájlként exportálják.

A hivatkozó KQL vezérlőben használja a `todynamic` függvényt a JSON elemzéséhez és az egyes oszlopok eléréséhez.

## <a name="grid-cell-clicks"></a>Rács cellára kattintás

A munkafüzetek lehetővé teszik, hogy a szerzők interaktivitást adjanak egy speciális típusú `link renderer` , a A hivatkozás-megjelenítő egy rácsvonalat helyez át egy hiperhivatkozásba a cella tartalma alapján. A munkafüzetek számos különböző típusú csatolást támogatnak – többek között az erőforrás-áttekintő pengék, a tulajdonság-megjelenítők, az alkalmazás-elemzések keresése, a használat, a tranzakciók nyomon követése stb.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Az interaktivitás beállítása a rács cellájának kattintással

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A _lekérdezés hozzáadása_ hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát _naplóként_, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` az eredmények megtekintése
6. Válassza ki az _oszlop beállításait_ a beállítások ablaktábla megnyitásához.
7. Az _oszlopok_ szakaszban állítsa be a következőket:
    1. _Minta_ -oszlop megjelenítő: `Link` , nézet megnyitása: `Cell Details` , hivatkozás címkéje: `Sample`
    2. _Count_ -Column megjelenítő: `Bar` , színpaletta: `Blue` , minimális érték: `0`
    3. _Kérelem_ -oszlop megjelenítő: `Automatic`
    4. A módosítások alkalmazásához kattintson a _Mentés és bezárás_ gombra.

    ![Képernyőkép az oszlop beállításai lapról.](./media/workbooks-interactive/column-settings.png)

8. Kattintson a `Sample` rács egyik hivatkozására. Ekkor megnyílik egy ablaktábla, amely egy mintavételi kérelem részleteit ismerteti.

    ![A mintául szolgáló kérelem részletes ablaktáblájának képernyőképe.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Renderelési műveletek csatolása

| Művelet csatolása | Művelet kattintáskor |
|:------------- |:-------------|
| `Generic Details` | A sortulajdonság-környezet lapon lévő sorok értékeinek megjelenítése |
| `Cell Details` | A cella értékét jeleníti meg a tulajdonságok rácsa lapon. Hasznos, ha a cella tartalmaz egy dinamikus típust (például a JSON-t a kérelem tulajdonságaival, például a helytel, a szerepkör-példánnyal stb.). |
| `Cell Details` | A cella értékét jeleníti meg a tulajdonságok rácsa lapon. Hasznos, ha a cella tartalmaz egy dinamikus típust (például a JSON-t a kérelem tulajdonságaival, például a helytel, a szerepkör-példánnyal stb.). |
| `Custom Event Details` | Megnyitja a Application Insights keresés részleteit az egyéni eseményazonosító ( `itemId` ) alapján a cellában. |
| `* Details` | Az egyéni esemény részleteihez hasonlóan, a függőségek, a kivételek, az oldalletöltések, a kérések és a Nyomkövetések kivételével. |
| `Custom Event User Flows` | Megnyitja a cellában az egyéni esemény nevében felmutatott Application Insights Felhasználókövetési élményt. |
| `* User Flows` | Az egyéni eseményekhez hasonlóan Felhasználókövetés a kivételek, az oldalletöltések és a kérelmek kivételével |
| `User Timeline` | A felhasználó idővonalának megnyitása a felhasználói AZONOSÍTÓval (user_Id) a cellában |
| `Session Timeline` | Megnyitja a cella értékének Application Insights keresési élményét (például az "ABC" szöveg keresése, ahol az ABC a cella értéke) |
| `Resource overview` | Nyissa meg az erőforrás áttekintését a portálon a cella erőforrás-azonosító értéke alapján |

## <a name="conditional-visibility"></a>Feltételes láthatóság

A munkafüzet lehetővé teszi, hogy a felhasználók bizonyos vezérlőelemek megjelenjenek vagy eltűnnek a paraméterek értékei alapján. Ez lehetővé teszi a szerzők számára, hogy a jelentések eltérőek legyenek a felhasználói bevitel vagy a telemetria állapot alapján. Egy példa arra, hogy a felhasználók csak egy összefoglalót jelenítenek meg, ha a dolgok jók, de a teljes részleteket megmutatják, ha valami nem stimmel.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Az interaktivitás beállítása feltételes láthatósággal

1. A két interaktív vezérlő beállításához kövesse az [interaktivitás beállítása a rácson sorban kattintson a szakasz elemre](#setting-up-interactivity-on-grid-row-click) .
2. Új paraméter hozzáadása a tetején:
    1. Név: `ShowDetails`
    2. Paraméter típusa: `Drop down`
    3. Szükséges `checked`
    4. Adatok lekérése: `JSON`
    5. JSON-bemenet: `["Yes", "No"]`
    6. Mentés a módosítások végrehajtásához.

    ![A paraméter hozzáadása gomb kiválasztása után megjelenik a paraméter szerkesztése panel.](./media/workbooks-interactive/edit-parameter.png)

3. Paraméter értékének beállítása `Yes`

    ![A kész szerkesztési gomb felett az a legördülő lista, amely lehetővé teszi a paraméter értékének megadását](./media/workbooks-interactive/set-parameter.png)

4. A lekérdezés vezérlőelemben a körzet diagramon válassza a _Speciális beállítások_ ikont (fogaskerék ikon)
5. A beállítás bejelölése `Make this item conditionally visible`
    1. Ez az elem látható, ha a `ShowDetails` paraméter értéke `equals``Yes`
6. A módosítások _elvégzéséhez válassza a Szerkesztés kész_ lehetőséget.
7. Olvasási mód beviteléhez válassza a munkafüzet eszköztárának _kész szerkesztés_ elemét.
8. Állítsa a paraméter értékét a következőre: `ShowDetails` `No` . Figyelje meg, hogy az alábbi diagram eltűnik.

Az alábbi képen látható, hogy hol `ShowDetails` van `Yes`

![A diagram látható feltételes láthatóságát ábrázoló képernyőkép](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

Az alábbi képen látható a rejtett eset, ahol a `ShowDetails``No`

![A diagram elrejtésének feltételes láthatóságát ábrázoló képernyőkép](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Több választási lehetőség a rácsokban és diagramokban

A lekérdezési és mérőszámok lépései egy vagy több paramétert is exportálhatunk, ha sor (vagy több sor) van kiválasztva.

![Képernyőfelvétel: az exportálási paraméterek beállításai több paraméterrel. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. A rácsot megjelenítő lekérdezési lépésben lépjen a speciális beállítások elemre.
2. Jelölje be a `When items are selected, export parameters` jelölőnégyzetet. További vezérlők is megjelennek.
3. Jelölje be a `allow selection of multiple values` jelölőnégyzetet.
    1. A megjelenített vizualizáció lehetővé teszi a többszörös kiválasztású és az exportált paraméterek értékeinek tömbjét, például a többszörös kijelölésű legördülő paraméterek használata esetén.
    2. Ha nincs bejelölve, a megjelenítési vizualizáció csak az utolsó kijelölt elemet veszi figyelembe. Egyszerre csak egyetlen érték exportálása.
4. Az összes exportálni kívánt paraméternél használja a *paraméter hozzáadása* gombot. Ekkor megjelenik egy előugró ablak, amely az exportálandó paraméter beállításait tartalmazza.

Ha az egyszeres kijelölés engedélyezve van, a szerző megadhatja, hogy az eredeti exportálás melyik mezője legyen exportálva. A mezők közé tartozik a paraméter neve, a paraméter típusa és az alapértelmezett érték, ha nincs kiválasztva (nem kötelező).

Ha a többszörös kijelölés engedélyezve van, a szerző meghatározza, hogy az eredeti exportálás melyik mezője legyen exportálva. A mezők tartalmazzák a paraméter nevét, a paraméter típusát, az idézőjelet és a elválasztót. A és a elválasztó értékkel rendelkező idézőjelek akkor használatosak, amikor egy lekérdezésben lecserélik a nyíl értékeket szövegre. A többszörös kijelölés lehetőségnél, ha nincs kiválasztva érték, akkor az alapértelmezett érték egy üres tömb.

> [!NOTE]
> A többszörös kijelölés esetében csak az egyedi értékek lesznek exportálva, a kimeneti tömb értékei (például az "1, 1, 2, 1") nem jelennek meg a kimeneti értékekként "1, 2".

Az exportálási beállítások területen hagyja üresen az "exportálandó mező" beállítást. Ha így tesz, az összes rendelkezésre álló mező a kulcs: érték párok sztringesített JSON-objektumként lesz exportálva. A rácsok és címek esetében ez a rács összes mezője lesz. A diagramok esetében az elérhető mezők x, y, sorozat és címke lesz (a diagram típusától függően).

Habár az alapértelmezett viselkedés egy paraméter szövegként való exportálása, ha tudja, hogy a mező egy előfizetés vagy egy erőforrás-azonosító, ezt használja az exportálási paraméter típusaként. Ez lehetővé teszi, hogy a paraméter olyan helyeken legyen használatban, amelyekhez ilyen típusú paramétereket kell megadni.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](./workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](./workbooks-access-control.md) és megosztása.