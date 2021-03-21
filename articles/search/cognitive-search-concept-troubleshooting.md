---
title: Tippek a mesterséges intelligenciának kialakításához
titleSuffix: Azure Cognitive Search
description: Tippek és hibaelhárítás az AI-bővítési folyamatok Azure-Cognitive Search történő beállításához.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 5882cc949d88e8c2a4102362cf5d2a3613e1d714
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99475488"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tippek az AI-bővítéshez az Azure Cognitive Search

Ez a cikk azokat a tippeket és trükköket tartalmazza, amelyek az Azure Cognitive Search AI-bővítési képességeinek megismeréséhez szükségesek. 

Ha még nem tette volna meg, ugorjon végig az [oktatóanyagban: Ismerje meg, hogyan hívhatja meg az AI-gazdagító API-kat](cognitive-search-quickstart-blob.md) a mesterséges intelligenciák blob-adatforrásokra való alkalmazásával.

## <a name="tip-1-start-with-a-small-dataset"></a>1. tipp: Kezdés kis adatkészlettel
A problémák gyors megtalálásának legjobb módja az, hogy növelje a hibák elhárításának sebességét. Az indexelési idő csökkentésének legjobb módja az indexelt dokumentumok számának csökkentése. 

Első lépésként hozzon létre egy adatforrást mindössze néhány dokumentum/rekord használatával. A dokumentum mintájának jól láthatónak kell lennie az indexelt dokumentumok különböző részein. 

Futtassa a dokumentum mintáját a végpontok közötti folyamaton, és győződjön meg arról, hogy az eredmények megfelelnek az igényeinek. Ha elégedett az eredménnyel, további fájlokat adhat hozzá az adatforráshoz.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>2. tipp: Ellenőrizze, hogy helyesek-e az adatforrás hitelesítő adatai
Az adatforrás-kapcsolatok nem lettek érvényesítve, amíg meg nem határoz egy olyan indexelő, amelyik azt használja. Ha olyan hibák jelennek meg, amelyek megemlítik, hogy az indexelő nem tudja beolvasni az adatvesztést, ügyeljen a következőre:
- A kapcsolatok karakterlánca helyes. Ha SAS-jogkivonatokat hoz létre, akkor ügyeljen arra, hogy az Azure Cognitive Search által várt formátumot használja. A különböző támogatott formátumok megismeréséhez lásd: [hitelesítő adatok megadása szakasz](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) .
- Az indexelő tárolójának neve helyes.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>3. tipp: mi működik, még akkor is, ha vannak hibák
Időnként előfordulhat, hogy egy kis hiba leállítja az indexelő a zeneszámokban. Ez rendben van, ha egyenként szeretné kijavítani a problémákat. Előfordulhat azonban, hogy figyelmen kívül hagyja az adott típusú hibát, így az indexelő továbbra is megtekintheti, hogy milyen folyamatok működnek valójában.

Ebben az esetben érdemes megállapítania, hogy az indexelő figyelmen kívül hagyja a hibákat. Ezt úgy teheti meg, hogy a *maxFailedItems* és a *maxFailedItemsPerBatch* értéket a-1 értékre állítja be az indexelő definíciójának részeként.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> Ajánlott eljárásként állítsa be a maxFailedItems, maxFailedItemsPerBatch 0-ra az éles számítási feladatokhoz

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>4. tipp: hibakeresési munkamenetek használata a készségkészlet kapcsolatos problémák azonosításához és megoldásához 

A hibakeresési munkamenetek egy olyan vizualizációs szerkesztő, amely a Azure Portal meglévő készségkészlet működik. Hibakeresési munkameneten belül azonosíthatja és elháríthatja a hibákat, ellenőrizheti a módosításokat, és véglegesítheti a változtatásokat egy éles készségkészlet az AI-bővítési folyamatban. Ez egy előzetes verziójú funkció, amely [elolvassa a dokumentációt](./cognitive-search-debug-session.md). További információ a fogalmakról és az első lépésekről: [hibakeresési munkamenetek](./cognitive-search-tutorial-debug-sessions.md).

A hibakeresési munkamenetek egyetlen dokumentumon működnek, így a iteratív összetettebb bővítési folyamatokat hozhat létre.

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>5. tipp: dúsított dokumentumok megtekintése a motorháztető alatt 
A dúsított dokumentumok a dúsítás során létrehozott ideiglenes struktúrák, majd a feldolgozás befejezésekor törlődnek.

Ha pillanatképet szeretne készíteni az indexelés során létrejött bővített dokumentumról, adja hozzá az indexhez az ```enriched``` mezőt. Az indexelő automatikusan hozzáadja a mezőhöz az adott dokumentum bővítéseinek sztringes leképezését.

Az ```enriched``` mező egy sztringet tartalmaz, amely a JSON-ban szereplő memóriabeli bővített dokumentum logikai leképezése.  A mező értéke azonban egy érvényes JSON-dokumentum. Mivel az idézőjelek előtt escape-karakter áll, a `\"` karaktereket `"` karakterre kell cserélnie, ha a dokumentumot formázott JSON-ként szeretné megtekinteni. 

A dúsított mező csak hibakeresési célokat szolgál, így könnyebben megismerheti annak a tartalomnak a logikai formáját, amellyel a kifejezéseket kiértékeli a rendszer. Ez a mező nem függhet indexelési célokra.

Adjon hozzá egy ```enriched``` mezőt az index definíciójának részeként a hibakereséshez:

#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>6. tipp: a várt tartalom nem jelenik meg

A hiányzó tartalom az indexelés során eldobott dokumentumok eredménye lehet. Az ingyenes és az alapszintű csomagok esetében a dokumentumok mérete alacsony. Az indexelés során a korlátot meghaladó fájlok el lettek dobva. Az eldobott dokumentumokat a Azure Portalban tekintheti meg. A Search szolgáltatás Irányítópultján kattintson duplán az indexelő csempére. Tekintse át a sikeres dokumentumok indexelésének arányát. Ha az érték nem 100%, akkor további részletekért kattintson az arányra. 

Ha a probléma fájlmérethez kapcsolódik, a következőhöz hasonló hibaüzenet jelenhet meg: "a blob \<file-name> " mérete \<file-size> bájt, ami túllépi a jelenlegi szolgáltatási szinten a dokumentumok kinyerésének maximális méretét. " Az indexelő korlátaival kapcsolatos további információkért lásd: [szolgáltatási korlátok](search-limits-quotas-capacity.md).

A tartalom nem jelenik meg egy második oka, hogy kapcsolódó bemeneti/kimeneti leképezési hibák merülhetnek fel. Például a kimeneti cél neve "People", de az index mező neve kisbetűs "emberek". A rendszer 201 sikeres üzeneteket adhat vissza a teljes folyamathoz, így úgy gondolja, hogy sikeres volt az indexelés, ha valójában egy mező üres. 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>7. tipp: a feldolgozás kiterjesztése a maximális futási idő után (24 órás időszak)

A képelemzés még egyszerű esetekre is számításba vehető, így ha a képek különösen nagyok vagy bonyolultak, a feldolgozási idő túllépheti a maximálisan engedélyezett időt. 

A maximális futási idő a következő szintektől függ: több perc az ingyenes szinten, a számlázható rétegek 24 órás indexelése. Ha a feldolgozás nem hajtható végre egy 24 órás időszakon belül az igény szerinti feldolgozáshoz, váltson ütemezésre, hogy az indexelő feldolgozza a feldolgozást, ahol abbahagyta. 

Az ütemezett indexek esetében az indexelés az utolsó ismert jó dokumentumon folytatja az ütemezést. Ismétlődő ütemezés használatával az indexelő az összes nem feldolgozott lemezkép feldolgozásának idejére áttekintheti az adott lemezképen keresztüli várakozó képeket. Az ütemterv szintaxisával kapcsolatos további információkért lásd: [az indexelő beosztása](search-howto-schedule-indexers.md).

> [!NOTE]
> Ha egy indexelő egy bizonyos ütemezésre van beállítva, de többször is meghibásodik ugyanazon a dokumentumon, és minden egyes futtatásakor újra leáll, az indexelő egy ritkábban (legfeljebb legalább 24 óránként) fog futni, amíg a folyamat újból el nem végzi a műveletet.  Ha úgy gondolja, hogy rögzítette a problémát, amely miatt az indexelő egy bizonyos ponton elakadt, igény szerint futtathatja az Indexelő szolgáltatást, és ha a folyamat sikeresen elvégezte az előrehaladást, az indexelő az ütemezési intervallumba ismét visszatér.

A portálon alapuló indexeléshez (a gyors útmutatóban leírtak szerint) a "Futtatás egyszer" indexelő beállítás a feldolgozást 1 órára korlátozza ( `"maxRunTime": "PT1H"` ). Előfordulhat, hogy hosszabb időre szeretné kiterjeszteni a feldolgozási ablakot.

## <a name="tip-8-increase-indexing-throughput"></a>8. tipp: az indexelési teljesítmény növelése

[Párhuzamos indexeléshez](search-howto-large-index.md)helyezze az adatait több tárolóba vagy több virtuális mappába ugyanabban a tárolóban. Ezután hozzon létre több adatforrás és indexelő párokat. Az összes indexelő használhatja ugyanazt a készségkészlet, és az azonos keresési indexbe írhat, így a keresési alkalmazásnak nem kell megismernie ezt a particionálást.

## <a name="see-also"></a>Lásd még

+ [Rövid útmutató: AI-dúsítási folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: az AI-gazdagítás REST API-k megismerése](cognitive-search-tutorial-blob.md)
+ [BLOB-indexek konfigurálása](search-howto-indexing-azure-blob-storage.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [A dúsított mezők indexhez való leképezése](cognitive-search-output-field-mapping.md)
