---
title: Azure Monitor a Designer és a munkafüzetek közötti áttérési útmutató megtekintése
description: Átmenet a nézetekről a Azure Monitor munkafüzetekbe.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d6731673c2b9918b3b19d14f45d51a6424ef86f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619428"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor a Designer és a munkafüzetek közötti áttérési útmutató megtekintése
A [tervező](view-designer.md) a Azure monitor egyik funkciója, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti a log Analytics-munkaterületen lévő, diagramokkal, listákkal és időpontokkal kapcsolatos adatait. A rendszer átváltotta a munkafüzeteket, hogy rugalmas vászonra legyen szükség az adatelemzéshez és a Azure Portalon belüli, gazdag vizualizációs jelentések létrehozásához. Ebből a cikkből megtudhatja, hogyan lehet áttérni a tervezőből a munkafüzetekbe. 


## <a name="workbooks-overview"></a>Munkafüzetek áttekintése
A [munkafüzetek](../insights/vminsights-workbooks.md) szövegeket, [naplókat](/azure/data-explorer/kusto/query/), metrikákat és paramétereket egyesítenek gazdag interaktív jelentésekben. A csoporttagok ugyanazzal az Azure-erőforrásokkal való hozzáféréssel is szerkeszthetők a munkafüzetek.

A munkafüzetek hasznosak a következő forgatókönyvekhez:

-   A virtuális gép használatának megvizsgálása, ha nem ismeri a kamat metrikáit: CPU-kihasználtság, lemezterület, memória, hálózati függőségek stb. A többi használati elemzési eszköztől eltérően a munkafüzetek több fajta vizualizációt és elemzést is lehetővé tesznek, így nagyszerűen használhatók az ilyen típusú, ingyenes űrlapos feltáráshoz.
-   Elmagyarázza a csapatának, hogy a közelmúltban kiépített virtuális gép hogyan végezze el a teljesítménymutatók megjelenítését a főszámlálók és más naplózási események mérőszámai alapján.
-   Megoszthatja a virtuális gép átméretezési kísérletének eredményét a csapat más tagjaival. Megadhatja a kísérlet szöveggel kapcsolatos céljait, majd megjelenítheti a kísérlet kiértékeléséhez használt egyes használati metrikákat és elemzési lekérdezéseket, valamint egyértelmű meghívásokat, hogy az egyes metrikák a cél felett vagy alatt voltak-e.
-   A leállás következményeinek jelentése a virtuális gép használatáról, az adatok összevonásáról, a szöveg magyarázatáról, valamint a következő lépések megvitatására a leállások jövőbeli megelőzése érdekében.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Miért érdemes konvertálni a Designer-irányítópultokat a munkafüzetekbe?

A Designer segítségével különböző lekérdezés-alapú nézeteket és vizualizációkat hozhatnak elő. Számos általános testreszabási lehetőség azonban továbbra is korlátozott. Ezek közé tartozik a rácsok és csempék elrendezésének formázása, illetve az alternatív grafikus elemek kiválasztása az adatok szemléltetéséhez. A Tervező nézet legfeljebb kilenc különböző csempére korlátozódik, amelyek az Ön adatait képviselik.

A Munkafüzetek egy olyan platform, amellyel felszabadíthatja az adatokban rejlő teljes potenciált. a munkafüzetek nem csak az összes képességet őrzik meg, de további funkciókat is támogatnak szöveg, mérőszámok, paraméterek és sok más funkció segítségével. A munkafüzetek például lehetővé teszik a felhasználók számára a sűrű rácsok összevonását és a keresési sávok hozzáadását az adatszűréshez és az adatelemzéshez. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>A munkafüzetek a nézet tervezővel való használatának előnyei

* A a naplók és a metrikák használatát is támogatja.
* Az egyéni hozzáférés-vezérléshez és a közös munkafüzetek nézeteihez is lehetővé teszi a személyes nézetek használatát.
* Egyéni elrendezési beállítások tabulátorokkal, méretezéssel és méretezési vezérlőkkel.
* Több Log Analytics-munkaterület, Application Insights-alkalmazások és-előfizetések közötti lekérdezés támogatása.
* Engedélyezi az egyéni paramétereket, amelyek dinamikusan frissítik a társított diagramokat és vizualizációkat.
* A sablonok katalógusának támogatása a nyilvános GitHubon.

Habár ez az útmutató egyszerű lépésekkel hozza létre a gyakran használt View Designer-nézetek számos különböző nézetét, a munkafüzetek lehetővé teszik a felhasználók számára a saját egyéni vizualizációk és mérőszámok létrehozásának és kialakításának szabadságát. A következő képernyőkép a munkaterület- [használati sablonból](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) származik, és példát mutat arra, hogy milyen munkafüzetek képesek létrehozni:


![Munkafüzet-alkalmazás – példa](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>A munkafüzetek használatának megkezdése
A munkafüzetekben lévő munkafüzetek megnyitásának engedélyezése Log Analytics munkaterületeken az oldalsó navigációs sávon elemként, közvetlenül a tervezői hely alatt.

![Navigációs munkafüzetek](media/view-designer-conversion-overview/workbooks-nav.png)

A kiválasztást követően megjelenik egy katalógus, amely megjeleníti a munkaterület összes mentett munkafüzetét és sablonját.

![Munkafüzetek gyűjteménye](media/view-designer-conversion-overview/workbooks-gallery.png)

Új munkafüzet indításához kiválaszthatja az **üres** sablont a **gyors üzembe helyezés** alatt, vagy az **új** ikont a felső navigációs sávon. A sablonok megtekintéséhez vagy a mentett munkafüzetekbe való visszatéréshez válassza ki az elemet a katalógusból, vagy keresse meg a nevet a keresősávban.

A munkafüzet mentéséhez a jelentést egy adott címmel, előfizetéssel, erőforráscsoporthoz és hellyel kell mentenie.
A munkafüzet a LA munkaterület ugyanazokkal a beállításokkal jelenik meg, mint az előfizetés, az erőforráscsoport, azonban a felhasználók megváltoztathatják ezeket a jelentési beállításokat. Alapértelmezés szerint a munkafüzetek *saját jelentések*, csak az egyes felhasználók számára lesznek mentve. Emellett közvetlenül is menthetők a megosztott jelentésekhez, vagy később is megoszthatók.

![Mentett munkafüzetek](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Következő lépések

- [Átalakítási beállítások](view-designer-conversion-options.md)